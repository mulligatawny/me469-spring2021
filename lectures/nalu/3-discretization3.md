# CVFEM discretization in detail

## Note: Associated slide deck is `me_469_handout_discretization_cvfem.pdf`

We iterate over locally-owned elements for both time/source and advection/diffusion terms. We have a choice here too: we could assemble the dual volumes to the nodes and iterate over nodes without losing accuracy. However, if you do that for the time/source, you get the connected nodes involved, which is called the **consistent mass matrix**, which reduces the error (not order of accuracy) a bit.

### Element loops

Isoparametric coordinates are used to define Lagrangian basis functions within each element, for the quadrature point evaluation.

### Implicit time integration

The time terms are evaluated at the volume quadrature points. Again, the connected nodes are coupled, leading to full node:element:node connectivity.

Look at `ScalarMassElemKernel.C`.

### Source term discretization

Source terms are similar to the above. (The assembly to the nodes is very similar to the filtered_filter class I looked at).

### Advection discretization

Interestingly, the integration by parts is not necessary. It is strange to think of an advection term over the volume rather than at the surfaces as a flux, and even in CVFEM it is not common, but note that it is not required here, unlike in FVM, where you don't have the option to not apply Gauss divergence.

Volume terms in CVFEM usually come from an underlying non-conserved form.

In CVFEM, the control over the discretization and accuracy are embedded in the basis. We have moved away from the idea of a stencil and QQUICK and all of that, to now simply picking a different basis functions. The process is still loops over quadrature nodes, but the critical distinction is in the construction of the method.

In Nalu, there is still *only one implementation* for the advection, diffusion etc., but the quadrature rule is different! If we have a mix of elements, we can iterate over blocks, get the master element type, get its quadrature points, and build a general advection operator. See `SteadyThermal3dContactSrcElemKernel.C`, which is a template class.

### Diffusion discretization

This is the first (and only) term where we need to compute derivatives. Using Gauss divergence reduces the order of the derivative by one. You transform the integral into a loop (sum) over nodes, with the derivatives of the shape functions involved (see notes).

The CVFEM approach does not have any non-orthogonality corrections.

At high aspect-ratio flows - like boundary layer inflations - the diffusion operator from theory becomes non-monotic. In combination with high gradients, you may see oscillations. A way around it is to move the surface quadrature point to the midpoint of the edges. See Leonard's paper from Files. The theoretical values of aspect ratios at which this happens is ridiculously low (sqrt(2)). Consider compressible boundary layers as a good example where this happens in a resolved boundary layer. When people first saw this, incidentally, they blamed it on finite elements and wanted to go back to finite volumes.

Upgrading the linear basis to quadratic would involve moving from 5 to 9 points. 

### Nodal gradients again

We needed these in the cell-centered context and got them from Green-Gauss or Least Squares. Here is another view for CVFEM:

The gradients at the edge midpoints are differnt depending on which element side you use. Thus, we view the gradient at continuous at the nodes, and discontinuous inside the elements. This difference is minimized (see slide 88). 

### Is CVFEM conservative by construction?

Yes. If you had to evaluate a flux at a surface quadrature node, you have to define an L and R states. This flux leaving L enters R exactly. The assembly of fluxes is telescopic - stuff cancels itself out and you are left with out minus in.
