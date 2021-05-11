# Comparison between EBVC and CVFEM for flow past cylinder with hybrid mesh

Note that in Tri3 mesh, the stencil is identical for both CVFEM and EBVC, while CVFEM has a larger stencil for the Quad4.

The viscous force in the .dat files is the viscous drag, so you need to sum contributions from both forces for total (form + viscous) drag. 

Temporal accuracy appears to be more important than mesh resolution in this case.

The use of the limiter reduces the amount of clipping in the mixture fraction. Clipping isn't a good thing to do from a non-linear convergence perspective, since the solution might want to be oscillatory. Thus, we might not clip the values exactly to enforce realizability, but when you compute material properties like density, you have no choice but to enforce them.

The OPEN BC does not handle returning or entrainment, and needs a far field effect to be accounted for. The open also says that static or total pressure has to be constant, with zero normal gradients for velocity. Sometimes people will put a "sponge layer" to straighten the flow at the outlet so you are not violating the boundary condition.

Another way to do this is a convective boundary. 

One interesting observation is that if you have a symmetric inflow with a symmetric grid and symmetric BCs, the solution will not start shedding vortices until the flow hits the outflow. In the midterm mesh, the unstructred grid causes the shedding to trigger. In a purely symmetric mesh, the shedding could be triggered late by just roundoff! Extraordinary.

# Pressure projection

We need an expression for the motion pressure (constant-in-space-variable-in-time-thermodynamic-pressure). 

This leads us to the Pressure Poisson equation which corrects the u_star obtained by solving momentum without regard for the pressure. One of the ways of arriving at the PPE is by taking the divergence of the velocity projected nodal gradient.

### Why pressure projection?

In general, any vector can be written in terms of a known divergence and a curl-free part (Hodge decomposition).

If the vector is u in an incompressible flow, the known divergence is zero.

See Domino 2006 CTR.

When the known divergence is 0, it is an idempotent projection (P\*\*2 = P). When it is non-zero, it is an affine-projection operator.

The NS momentum can be thought of in this framework in the zero Mach limit.

# Diagonal dominance 

If you violate the diagonal dominance condition for the linear solvers, your standard preconditionrs (SGS) will not converge quickly. You can fix this by reducing the time step size so that the Courant is 1, but this does not fix the problem of the non-monotonicity of the operators.
