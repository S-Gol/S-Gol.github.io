## Generation of a polygonal grid on a sphere

![Hexagonal Grid](/Images/PolyGrid/Grid1.PNG)

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

![Icosahedron](/Images/PolyGrid/Icosahedron.svg)

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

![Subdivision Example](/Images/PolyGrid/Subdivision.svg)

After subdiving, we wind up with an icosphere. Again, Wikipedia provides a great example of what it should look like. 

![Subdivision Example](/Images/PolyGrid/IcosphereWikipedia.PNG)
