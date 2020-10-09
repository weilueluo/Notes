> Mainly summarize from http://blog.ezyang.com/2019/05/pytorch-internals/

### Tensor

A n-dimensional data structure containing some data and its metadata such as size, d-type, device, strides and layout.

**Stride** is a distinctive feature in pytorch, it is used to calculate the physical location in the memory. For example, a continuous tensor `t` of size `[2,2]` have a stride of `[2,1]`, this mean that if you are accessing position `t[1,0]`, then you can find the data location by calculating `1*2+0*1=2` (zero indexed). 

**Stride combined with offset** can be used to represent many different views, for example the first column `t[:, 0]` can be represented by stride `[2, ]` since there is only 2 elements in a row in this case.

**View** is a part of the tensor, for example when you do `t[1,:]`, you are retrieving the second row of this tensor, all tensor need to do in this case is to record the offset saying that the data of this tensor is between this offset. Note that if you want to free the underlying memory all you can do is make a copy of the view.

There is always a **Storage** associated with a tensor, even for simple case (might have changed), there can be many tensor sharing the same storage. Storage define the **physical size** and **dtype** while tensor define the **size**, **stride** and **offset** (the logical representation).

#### Tensor Accessor

It is a class for convenience class that handles tensor access calculation.

#### Tensor Operations

The input tensor is unwrapped (some don't, they just call other variable's implementations), then two dispatches happen. Dispatches are needed because implementations on CPU and CUDA (/ on float and int) are different. The first dispatch need to be dynamic because these kernels (?) may live in separate libraries.

> // what does this mean in the original text?
>
> "However, once you unwrap and go into the non-Variable Tensor universe, that's it; you never go back to Variable (except by returning from your function.)"

<img src="http://blog.ezyang.com/img/pytorch-internals/slide-12.png" alt="tensor operations dispatch" style="zoom: 33%;" />

#### Tensor Extensions

The current model (May 2019) uses three parameters to uniquely determine what is a tensor:

<img src="http://blog.ezyang.com/img/pytorch-internals/slide-14.png" alt="tensor extension" style="zoom:33%;" />

- The **device**, the description of where the tensor's physical memory is actually stored, e.g., on a CPU, on an NVIDIA GPU (cuda), or perhaps on an AMD GPU (hip) or a TPU (xla). The distinguishing characteristic of a device is that it **has its own allocator**, that doesn't work with any other device.
- The **layout**, which describes **how we logically interpret this physical memory**. The most common layout is a strided tensor, but sparse tensors have a different layout involving a pair of tensors, one for indices, and one for data; MKL-DNN tensors may have even more exotic layout, like blocked layout, which can't be represented using merely strides.
- The **dtype**, which describes what it is that is actually stored in each element of the tensor. This could be floats or integers, or it could be, for example, quantized integers.

##### [Sparse Tensor](https://stackoverflow.com/questions/47662143/what-is-the-difference-between-tensors-and-sparse-tensors)

A tensor can be called sparse if its values are mostly zero. It is given another name because it is faster to perform computation by iterating through non-zero elements only, mathematicians developed many special methods for operating on them.

For example `[0, 7, 0, 0, 8, 0, 0, 0, 0]` can be represent as:
- `values=[7, 8]`, store non-zero values.
- `indicies=[1, 4]`, store non-zero values' indices, more commonly written as `[[1], [4]]` for consistency over n-dim matrix.
- `dense_shape=[9]`, store total number of elements so that we know how many trailing zeros are there.

### Autograd

> A great [link](https://towardsdatascience.com/automatic-differentiation-explained-b4ba8e60c2ad) that explains auto differentiation for single value. 
>
> If you have a vector then the gradient should be the Jacobian Matrix, generally speaking, `torch.autograd` is an engine for computing vector-Jacobian product.

Pytorch implements auto gradient by storing extra information in the tensor, such as `AutogradMeta`, `grad_fn`. Using these metadata, the engine of the autograd act as a parallel graph executor to compute the gradient.

<img src="http://blog.ezyang.com/img/pytorch-internals/slide-26.png" alt="autograd engine" style="zoom:33%;" />

You can compute the gradient like the blue lines:

<img src="http://blog.ezyang.com/img/pytorch-internals/slide-18.png" alt="without autograd" style="zoom:33%;" />

But this is tedious and autograd provide a way to find the leaf variable's gradient easily by simply calling `loss.backward()`, the result will be stored in the `grad` variable (leaf variable only).

##### [Determinant](https://www.youtube.com/watch?v=Ip3X9LOh2dk) (Extra)

When you have a linear transformation, you are scaling the original space, the amount you scale is called **determinant**. For example:
$$
det
\begin{pmatrix}
3&2\\
0&2
\end{pmatrix}
=6\\
det
\begin{pmatrix}
-1&1\\
-1&-1
\end{pmatrix}
=2\\
det
\begin{pmatrix}
4&2\\
2&1
\end{pmatrix}
=0\\
\text{*0 because it squishes things into smaller dimension}
$$
But note that if the transformation flip the space over, then it has negative determinant (aka invert the orientation of space). For example
$$
det
\begin{pmatrix}
2&1\\
-1&-3
\end{pmatrix}
=-5
$$
For 3D transformation, if the determinant is $0$, that means the dimension is squeezed into 2 or 1, this also means that the vectors define the 3D space are now linear dependent.

### Mechanics & Writing Kernels & Workflow Efficiency

// skipped, because I want to get some insights but not contributing to pytorch yet.

