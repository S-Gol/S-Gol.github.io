## Generation of a polygonal grid on a sphere

![Hexagonal Grid](/Images/PolyGrid/Grid1.PNG)

### Introduction

#### In Academia 

Properly discretizing shapes is important for a variety of applications, from structural analysis to geologic simulations. In order to simulate planetary phenomenon, a properly discretized sphere is needed. These planetary simulations include things like tectonic motion, weather, and climatology. 

#### In Game Dev 

Strategy games such as the Civilizations franchise have long used hexagonal grids to represent their world. These grids, however, represent a cylindrical world rather than spherical - This is obvious when trying to travel around north and south poles, for example. By wrapping a grid around an actual sphere, these effects can be corrected. 

### Generation of the grid

There are two techniques typically used for this type of grid. 

#### Voronoi / Delauney Generation

This technique uses the Voronoi diagram on a grid of points to create polygonal shapes. The boundaries are defined by the Voronoi edges, while the Delauney triangulation forms the grid of neighbors. 

#### Icospere 

This technique uses a subdivided icosahedron to create faces. Faces are subdivided from the triangles of the initial shape, and then formed into hex- and pent-agons in a later step.  
