## Generation of a polygonal grid on a sphere

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/Grid1.PNG" width="500">
</p>

### Introduction

#### In Academia 

Properly discretizing shapes is important for a variety of applications, from structural analysis to geologic simulations. In order to simulate planetary phenomenon, a properly discretized sphere is needed. These planetary simulations include things like tectonic motion, weather, and climatology. 

#### In Game Dev 

Strategy games such as the Civilizations franchise have long used hexagonal grids to represent their world. These grids, however, represent a cylindrical world rather than spherical - This is obvious when trying to travel around north and south poles, for example. By wrapping a grid around an actual sphere, these effects can be corrected. 

### Techniques

There are two techniques typically used for this type of grid. 

#### Voronoi / Delauney Generation

This technique uses the Voronoi diagram on a grid of points to create polygonal shapes. The boundaries are defined by the Voronoi edges, while the Delauney triangulation forms the grid of neighbors. 

#### Icospere 

This technique uses a subdivided icosahedron to create faces. Faces are subdivided from the triangles of the initial shape, and then formed into hex- and pent-agons in a later step.  

### Application

I used the icosphere technique. It provides a more regular grid than the Voronoi method, while avoiding the complicated code needed to compute Delauney triangulations in spherical coordinates. 

#### Generating the icosphere

I started with the implementation of icosphere generation presented on the [Unity Community Wiki.](http://wiki.unity3d.com/index.php/CreateIcoSphere) It generates a simple isocahedron, shown in this image provided by Wikipedia: 

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/Icosahedron.svg" width="500">
</p>

Following the initial creation of this shape, we have to subdivide it to achieve the appropriate number of triangles and vertices. 

We can predict the number of vertices (which will turn into polygons later) from the exponential equation `V=10.25*e^(1.383*s)`. This is a best-fit equation for gathered data, and may not be exact at higher subdivision. 

Based on our chosen number of subdivisions, we can then iterate through the list of triangles. This is performed using the same implementation as the Unity wiki.  

```// refine triangles
for (int i = 0; i < recursionLevel; i++)
{
	List<TriangleIndices> faces2 = new List<TriangleIndices>();
	foreach (var tri in faces)
	{
		// replace triangle by 4 triangles
		int a = getMiddlePoint(tri.v1, tri.v2, ref vertList, ref middlePointIndexCache, radius);
		int b = getMiddlePoint(tri.v2, tri.v3, ref vertList, ref middlePointIndexCache, radius);
		int c = getMiddlePoint(tri.v3, tri.v1, ref vertList, ref middlePointIndexCache, radius);

		faces2.Add(new TriangleIndices(tri.v1, a, c));
		faces2.Add(new TriangleIndices(tri.v2, b, a));
		faces2.Add(new TriangleIndices(tri.v3, c, b));
		faces2.Add(new TriangleIndices(a, b, c));
	}
	faces = faces2;
}
```
```
private static int getMiddlePoint(int p1, int p2, ref List<Vector3> vertices, ref Dictionary<long, int> cache, float radius)
{
	// first check if we have it already
	bool firstIsSmaller = p1 < p2;
	long smallerIndex = firstIsSmaller ? p1 : p2;
	long greaterIndex = firstIsSmaller ? p2 : p1;
	long key = ((smallerIndex & 0xFFFF0000) << 48) + ((greaterIndex & 0xFFFF0000) << 32) + ((smallerIndex & 0x0000FFFF) << 16) + (greaterIndex & 0x0000FFFF);

	int ret;
	if (cache.TryGetValue(key, out ret))
	{
		return ret;
	}

	// not in cache, calculate it
	Vector3 point1 = vertices[p1];
	Vector3 point2 = vertices[p2];
	Vector3 middle = new Vector3
	(
		(point1.x + point2.x) / 2f,
		(point1.y + point2.y) / 2f,
		(point1.z + point2.z) / 2f
	);

	// add vertex makes sure point is on unit sphere
	int i = vertices.Count;
	vertices.Add( middle.normalized * radius );

	// store it, return index
	cache.Add(key, i);

	return i;
	}
  ```
  
Note the changes to the key formula - the Unity implementation had a large number of hash collisions, slowing down the generation. Thanks to @Pulni on the GameDevLeague Discord for this solution. 

The subdivision in the code above shares points between triangles. While this does not allow for proper rendering as the vertices would share normals and UVs, this is vital for the truncation operation later. The code performs the operation shown in the image below.

1. Get the original triangle
2. Calculate midpoints, after checking if the adjacent triangle has already done this
3. Discard the original triangle, create four new ones based on the midpoints. 

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/Subdivision.svg" width="500">
</p>

After subdiving, we wind up with an icosphere. Again, Wikipedia provides a great example of what it should look like. 

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/IcosphereWikipedia.png" width="500">
</p>

At this point, you might be thinking "But those are all pentagons! Where are the hexagons I was promised?" For geometrical reasons, the isocahedron is created of 12 pentagons. When we subidivide it, these pentagons are retained. We can convert the other triangles into hexagons, but these 12 pentagons will always exist - any size grid can be created, and these 12 will always be there, haunting you. If you have a problem with this, you can take it up with Euler or move to a hyperbolic world. 

#### Truncation

After we have the icosphere, we need to perform a [truncation operation](https://en.wikipedia.org/wiki/Truncation_(geometry)) to create the polygonal faces. In general, truncation takes the vertices of a mesh and "crushes" them inwards. This has the effect shown in the image below. 

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/CubicTruncation.png" width="500">
</p>

The ideal truncation for the hexagonal surface:

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/HexTruncation.svg" width="500">
</p>

The truncation is performed in 3 steps.

1. Calculate the centroid of each triangle (1 Parallel instance per triangle) 
2. Store all edges and triangle IDs to a dictionary (1 Parallel instance per triangle)
3. Create new edges (not triangles yet) for each truncated face (1 Parallel instance per vertex)

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/TruncSteps.svg" width="500">
</p>

#### Truncation step 1

This step is easy enough - just take the centroid or average of all three points of a triangle, and store them to an array somewhere indexed by the triangle's index

#### Truncation step 2

Step 2 is the important part here, and the core of this technique. When we store edges/tris in the dictionary, they are hashed according to the two vertices that designate the ends. This hash needs to be created such that it can be inverted to get the other triangle that shares it. In my case, the hash is calculated as below. 

```
public static long dirSpecHashCode(int a, int b)
{
    int i0, i1;
    long sign;
    if (a < b)
    {
	i0 = a;
	i1 = b;
	sign = 1;
    }
    else
    {
	i1 = a;
	i0 = b;
	sign = -1;
    }

    long key = 23;
    key = key * 314159 + (long)i0;
    key = key * 314159 + (long)i1;
    return key*sign;
}
```

By negating the hash, we can get the hash of the opposing triangle. Because we know that triangles are always wound clockwise in Unity, we know that this will always work. 

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/Triangles.svg" width="500">
</p>

Because of the winding, we can designate the triangle ABC as a collection of 3 edges, AB,BC,CA. Similarly, BDC is made up of BD,DC,CB. In the dictionary we created in step 2, we assigned the keys to be the edges and the values to be their parent triangles. In this case, the keys of hash(AB), hash(BC), hash(CA) will all return the value of the left triangle. 

Based on negative property of the hash, we can also reverse these hashes. For example, hash(BC) will return the *left* triangle, while hash(CB) will return the *right* triangle. 

#### Truncation Step 3

In step 3, these hashes are used to order the edges. Let's refer back to this image.

<p align="center">
<img src = "https://github.com/S-Gol/s-gol.github.io/blob/main/Images/PolyGrid/TruncSteps.svg" width="500">
</p>

In step 2, we have a polygon made up of points 0,1,2,3,4,5,6, with 6 being the center of the new hexagon. Step three follows the pseudocode below

```
Start: t = arbitrary connected triangle
List<points> orderedPoints
//Lets assume t = triangle 0,1,6 from the example
for i in connected triangle count:
	e = hash of the next CCW edge
	//In the 0,1,6, example, this would be the edge 6,1. 

	orderedPoints.append(centroid of t) 
	e=-e
	set t = dictionary value of e
```
This step can also be used to calculate the neighbors of each polygon by adding the vertice at the other end of the edge to a list. This is not needed for the pure grid generation, and is thus neglected.

#### Completion

After performing this truncation, we now have a set of polygons defined by a sorted list of their vertices. This list is sorted CW, and can be easily triangulated to an appropriate mesh format. 
