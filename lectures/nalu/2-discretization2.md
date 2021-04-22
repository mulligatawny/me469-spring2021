# EBVC discretization in detail

## Note: Associated slide deck is `me_469_handout_discretization_ebvc.pdf`

### Some review from the previous lecture

Refer to the Gresho Wiggles paper for FVM versus FEM.

Both EBVC and CVFEM have their quantities at the vertices (nodes). The thing about FVM is that it *does* have a test function - a Heaviside - and it *does* have an underlying basis - usually linear, see the structured FVM example, where we have a weighting between nodes.

In CVFEM, we have to loop over four (in slide 42) elements to collect contributions for one element (?).

In EBVC:
To reduce the stencil, we take the subcontrol volumes (dual nodal volumes) and assemble them to the nodes, after which we project them to the edjges, creating an edge-based stencil. 

The scvs are constructed by the midpoints of opposing edges. Each scv has a volume quadrature point where source/body terms and time derivatives are determined, and surface quadrature points where surface fluxes (integration by parts terms) are evaluated. Slides 43 and 44 show how EBVC is just a shifted standard CCFVM.

Hybrid (structured and unstructured at the same time) are common; for example, in wind turbine applications where the far field is structured, and the connections are handled by oversets. `Nalu-Wind` is moving down that path. 

Cell-centered schemes don't care whether you are 2D or one element thick, since you still have the same number of DOFs. The node-centered schemes in Nalu need BCs for one cell-thick meshes.

## Edge-based vertex-centered scheme

You iterate over edges (for integrated by parts terms) and nodes (volume/source/time terms).

### BDF2 time integration `ScalarMassBDF2NodeSuppAlg.C`

### Source terms `VariableDensityMixFracSrcNodeSuppAlg.C`

The number of quadrature points `N` is related to the polynomial order as `p = 2*N -1`, so 1 integration point gives you a linear basis.

### Surface terms `AssembleScalarEdgeSolverAlgorithm.C`

Unlike nodal terms, these require connectivity. We need the QOI at the surface integration points `phi_ip`. The quadrature point is EXACTLY in the middle of the edges, and this simplifies the weights (1/2, 1/2). 

If you only use a central Galerkin operator, we gain accuracy but can introduce oscillations. The upwind scheme simply takes the upwind edge value, and is therefore lower-order and also diffuse. 

An alternative is to compute the projected nodal gradient, which produces upwinding that is higher order, using a first-order Taylor's series "projection". You can then construct a blended scheme with the PNG and the Galerkin system and a blending coefficient. 

The gradient extrapolation is not bounded, and we will see later how they can be blended.

The complexity in unstructured grids is that we need to manually build the connectivity information, and this c.i. is transferred into the solver for use in the numerical methods. If you wanted to, you could ask for the nearest 100 nodes by a search, and that would give you all the information you need for a WENO or TVD-type scheme. Two reasons not to: (i) cost, (ii) you affect parallelism.
