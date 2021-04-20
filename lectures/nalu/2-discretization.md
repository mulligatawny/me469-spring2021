# Introduction to Meshing

## Note: Associated slide deck is `me_469_handout_discretization_intro.pdf`

Meshing a heart shape with a structured and orthogonal grid is not impossible, but introduces stair-stepping or poor representation of the geometry.

For canonical cases, there is really no reason not to have a Cartesian grid - plane channels, backward steps, etc. 

The application space determines the type of approach.

### Meshing a 2D heart with an unstructured grid

Consider a quad element: if you have a cell-centered scheme, you have a five-point stencil with face:element connectivity, and for a vertex-centered scheme, you have a patch of smaller elements (a dual-mesh) that has node:element connectivity.

In the vertex-centered scheme, we define a basis within each element (FEM).

### Topologies

A Hex8 element has 8 nodes and is shaped like a hexahedral, with linear elements (a linear basis).

You can promote these elements to Hex21, Hex27 etc. using a higher-order basis. This is similar to the QUICK scheme which achieves quadratic behavior by including neighboring elements. Here, we have the same basis within the same element. 

### Integrating of PDEs using test functions

In FVM, we usually use Gauss-Divergence to get surface integrals, which reduces the order of the second derivatives by one, critical if we have a linear basis (which cannot represent second derivatives exactly!). 

An alternative to remove the higher order derivatives is to multiply by an arbitrary test function (which needs to be differentiable at least once), and apply integration by parts. 

### CVFEM

(i) has a nodal basis for the QOI, which gives you a simply nodal gradient in each element (just the derivatives of the test functions), and (ii) still integrate by parts over the PDE, (iii) the test function is a Heaviside, with a derivative of a Dirac-Delta (which disappears at the boundaries of the control volume).

What we did was define a finite volume over which we integrate, where we have new surfaces, and we are conservative in each CV, but it also borrows the underlying basis from FEM which allows interpolation within each element, and arbitrarily promote these elements.

The promotion introduces additional DOF, which require more quadrature points to close, so you have to be careful about how we define accuracy.

The diffusion operator is free from non-orthogonality errors! 

Promotion is not a uniform refinement, rather the quadrature moves toward the Gauss-Lobatto collocation points, and the mesh spacing is variable over the element, so you need to use the DOF number to define accuracy.

The cost of the promotion is...cost. You still have bigger stencils within each element. 

The key improvement in CVFEM comes from the collocation points (?)

### EBVC

Starting with the dual mesh from CVFEM, we can get the edge-based scheme, which is very similar to a cell-centered FVM approach. The quardature points are summed up to the node, which is now the cell-center. This looks like the standard FVM but with the centers of the nodes shifted. 

The non-orthogonality of elements is an issue, since we only have linear elements.

EBVC allows for extreme generality; i.e. you only have an edge with an L & R state, and you eliminate the complexity of the mesh and its topologies (like the CCFVM)

In the element-based scheme, the integration rule and points depend on the shape of the elements.

### Staggered FVM

You keep the velocities on the faces, and pressures in the cell-centers.

The staggered schemes eliminate non-orthogonality, and remove checkerboarding in incompressible NS.

### Parallelism

When parallel decomposing, we need rules to define a mesh object. The owner of a mesh object (nodes, edges, faces, elements) is the lower-rank processor (based on processor number). This has implications for iterations: do you iterative over owned or shared elements?

You need a parallel reduction call to sum over different processes.

The other option is to ghost nodes, and simply iterate locally-owned nodes + ghosted.
