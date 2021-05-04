# Frequently-asked questions

### How do you compute a characteristic frequency?

You put a probe in the flow and compute the FFT of the time signal. Note that for a cylinder you cannot use the drag and lift instantaneous for a frequency, because (i) they are integrated quantities, and (ii) due to symmetry in the cylinder, the frequency will be a factor of 2 off for the drag and lift.

### What is a non-linear residual?

For a hydrodynamic simulation, we have pressure and velocites as QOI. At each timestep we have a set of non-linear iterations. 

### What is a limiter?

Recall that in an unstructured approach we are limited in how many neighbours we can access. We use PNGs to overcome this and create largers stencils. These use nodal gradients at j (made of j-1 and j+1) to project to integration point j+1/2. The limiter ensures the projection is a blending of the two j-1 and j+1. In Nalu, the limiter is van Leer (there are several others that are TVD flux-limiters).

### Why not FEM?

Limited support, with only `Tri3`, `Tet4`, `Hex8`...

If you run a mesh with `Quad4s`, you would get a missing grad ops element error. Thus, we need to implement the element types.

*A bit of history:*

In Sandia, there was/is a strong infrastructure for finite elements for structural analysis, and the Sierra framework had a good way to iterate elements. It did not allow for iteration over edges, or element faces. It was also hybrid-mesh oriented, so you have both hex and tet topologies, with element promotions (at least linear to quadratic).

When the low Mach code was developed, they wanted to be conservative for FVM methods, while also using the extant element infrastructure. They found a paper with CVFEM, which suit their needs.

There is an interest in moving toward general polyhedra in the future.

### What is the lumped / non-lumped time derivative?

See `FemMM.C` on Canvas. This is quite important. 

A broadened stencil usually brings higher accuracy and more headaches. The larger stencil for the time term removes diagonal dominance. This consistent mass matrix increases the numerical dispersion at the cost of some stability. Look up lumped v non-lumped. See slide 9.
