# FEM discretization in detail

## Note: Associated slide deck is `me_469_handout_discretization_cvfem.pdf`

We iterate over locally-owned elements for both source and advection terms. The first difference is that we have a single set of quadrature points for all terms, with no dual volumes. We integrate over the whole volume, which is an advantage for FEM, esp. as we move to higher order. In CVFEM, increasing the order increases the number of scvs, and the number of integration points can get out of hand.

The test function is now the weight function (Galerkin's method). 

Implict time, advection and diffusions discretization is similar to CVFEM. Again, we suffer from non-orthogonality.

### Numerical integration

We need to transform the variables from physical to isoparametric space, over a [-1, 1] mapping using Gauss quadrature.

Check `ScalarMassFemKernel.C` etc.

SPD has a paper showing that the performance of the CVFEM and the FEM are quite similar.
