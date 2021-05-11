# The Low-Mach Approximation

## Note: Relevant slide deck is me469_low_mach.pdf

We begin with the variable density (non-isothermal) equation of motion: mass, momentum and energy, which is a total of 5 equations in 3D. Our unknowns are 3 velocities, density, energy (in terms of enthalpy or temperature) and pressure. To close the system we add the equation of state for an ideal gas. This equation set meets all time and spatial length scales: i.e., we would see pressure waves moving through our domain if we are resolved enough.

### The GE in dimensionless form 

We see the appearance of Re, Pr and Ma as important dimensionless parameters. The viscous work and the kinetic energy are scaled by the square of the Mach number; thus they are less important at low Mach values.

More interestingly, the pressure term in the momentum equation has a Mach squared in the denominator. This is a singularity in the zero Mach limit. If you keep the equations as is, the equations become stiff at low Mach numbers.

Check slide 16 for the asymptotic expansion, and the notes.

We have made the density a function of the constant-in-space-variable-in-time thermodynamic pressure. 

### What does Low Mach mean in practice?

We have effectively filtered out the acoustics. With an elliptical pressure field, continuity is met instantly over any finite length (you can observe this in the first timestep of a simulation). We now have two pressures (thermodynamic pressure, and a motion pressure field). We also have no mechanism for energy change in an isothermal system.

Some applications where you could not use a Low Mach solver are: studying start-up transience in a domain where you are interested in acoustic wave propagation, and combustors where acoustic instabilities affect the flow. One way to solve these is to model the acoustics is using the Lighthill equation for post-processing but with no coupling with the solution.

Note: the acoustic pressure does not drive the flow, and so it does not affect the pressure. 


