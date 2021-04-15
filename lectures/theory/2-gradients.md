# Computing Gradients

In an unstructured grid, you have natural neighbours (just as you do in structured grids), but the neighbours *of neighbours* are not implicitly apparent! You need to build in this connectivity information explicitly (your grid generation tool will do it in practice). The key is that the lookup times are quite significant, which means that we prefer to use local information as much as possible, for flux/gradient evaluations.

### Convective flux computation 

For the upwinding scheme, there is no need of gradients: we simply use the direction of the wind and get the value, but when you use QUICK, you either need neighbours of neighbours (bad) or the gradient at the interface. 

### Diffusive flux computation

The form of the diffusive flux has a gradient in it anyway, and we need a way of evaluation for unstructured grids.

We need (du/dn) at the interface, and a simple finite difference will only give you (du/ds), where s is the direction joining the two cell centers. s and n are usually misaligned (unless your cells are perfectly orthogonal). To achieve this, we compute (du/ds) and add a correction. The correction requires the true gradient at the face, which can be the average of the gradients at both cells. In other words, you need both the velocity and the gradient at each node (nodal gradient). SPD: the optimal location for the quadrature point is shifted by non-orthogonal grids, and this is why we use edge-based schemes.

### Computing the gradients: Green-Gauss' and Least Squares

Use the Green-Gauss method to use u at the faces times the normal, integrated over all faces. If we have the cell-centered values, we can get the face-centered values, but we don't, so we need to iterate.

We can also use a Taylor's series extension to compute the gradient, and develop a least squares system. 

### Projected nodal gradients

Compute node values from the cell-centres, and construct accurate face-centred values, then get the gradient. These Green-Gauss nodal gradients can be computed using an area-weighted approach, for instance.

Note that we have shifted the flux calculation problem to the evaluation of the gradients. 

The problem with the PNG is there is no boundedness guarantee, and to address this, we will be look at edge-based schemes in the next lecture. Most production codes ignore orthogonality and allow the diffusion term to converge slowly, to get stability.

### ADE forms

In an unstructured grid, the matrix A has no real structure (the neighbours are wherever they happen to be depending on the ordering).


