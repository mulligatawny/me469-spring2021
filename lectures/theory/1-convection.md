# Advection Discretization

## SEE HANDOUT #3 FOR ACCOMPANYING SLIDES

In an upwinded solution, the LOE is diffusive, and the solution is inaccurate. In the centered scheme (with RK4), trailing oscillations are seen, owing to incorrect wave speed capture of the high frequency content in the IC. 

One may blend both schemes using a simple averaging to obtain an intermediate scheme.

### Conservation properties

FVM allows for naturally conservative schemes, i.e. the inegral of the QOI over time is a constant; it only varies in space. This is called primary conservation.

Secondary conservation is for the square of the QOI (i.e. higher order moments are conserved too). However, different schemes (Up, Ct, Blend) do not all have this property, just Ct: because of symmetry! This is important because energy (QOI squared) conservation allows you to get stable schemes.

### More advanced schemes: QUICK

Quadratic Upwind Interpolation for Convective Kinematics (used to be popular once but now relegated to textbooks), is upwinding but with one additional point upwind of the current face and one downwind, giving you a parabola. Note that upwinding gives you a piecewise interpolant; we skipped the linear interpolant, but the idea is the same. 

QUICK introduces complexity by using W and E in addition to cell P. In an unstructured mesh this will require you to have information from many neighbours, which is why this is not used much these days. Once you go through the algebra, you see that QUICK has a central difference term and a diffusion-type finite difference term, which is in the same spirit as in Lax, i.e. take a centered scheme and add a stabilization term.

An important thing to realize is that phi_P is a cell-based integral, and not the value at the cell center. Thus, if you have a higher order integration than mid-point, you need to apply cell-based integrals in the derivation of QUICK (which changes the coefficients slightly).

QUICK conserves the first moment, and _almost_ conserves the second moment (owing to imperfect symmetry). Look up Leonard's paper on QUICK.

An observation: in a generalized unstructured grid, it is difficult to get neighbours of neighbours (getting W for flux between P & E). This can be avoided by using two points and a gradient, rather than three points.

**Cost** In a 1D problem, QUICK ruins the tri-diagonal structure of your matrix, but (see `me469_notes.pdf`) if you reformulate the linear system to a residual type equation, you can still preserve structure.

### WENO

Essentially Non-Oscillatory schemes try to reduce spurious oscillations in regions of high gradients, by building polynomial reconstructions to interpolate the solution at the faces, and select the one leading to the smoothest solution. 

Define a "smoothness sensor" for each polynomial, and pick the smoothest. The cost of evaluation is much higher because of a large stencil, and the reconstruction is lower-order. WENO blends two (or more) different polynomials. In the most general case, we can almost consider a polynomial expansion with weights.

Note that we drop down to low-order polynomials in regions with small gradients, and the design order of accuracy really depends on how you compute the error metric (local versus global).
