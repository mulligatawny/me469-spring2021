# A comparison of the discretizations in practice

When you compare two schemes with similar discretizations (one FVM and one FEM) the errors are usually comparable, but the speed might vary.

CVFEM has a larger stencil and is slightly more accurate than the EBVC, even though they are both same order of accuracy. The first reason is because CVFEM in a hex8 element has 4 surface quadrature points, compared with just 1 for EBVC. The second reason is due to the better performance of the Laplace operator, which we will see later.

The channel flow is interesting because you have flowstructures near the wall that are aligned with structured meshes, but on the other wall with triangles, you are misaligned with the features, and this is deliberately done to mess with the flow.


