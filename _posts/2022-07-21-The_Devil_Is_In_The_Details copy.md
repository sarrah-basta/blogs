---
layout: post
title: "GSoC Progress Report : Weeks 4-5"
subtitle: "The Devil is in the Details"
date: 2022-07-21
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# Week 3 : Keeping the pace steady 
<br>
```
Before software can be resuable, it first has to be usable
                                        - Ralph Johnson
```

- **Getting the code to work** <br>
Throughout this week, I focused on bringing the work done previously to some fruition, i.e. actually being able to run the code written succesfully. 

Now, the operations I was talking about comprise of, in my skeletal code structure referencing to NVector_Serial, of upto 1900+ lines of code, and even reading that to understand which were required or not, and how they should be implemented, was quite overwhelming to begin with. But thankfully, programmers revert to good programming practices, and so did the ones writing this. 

Most of the code in all the different operations, accesses data from it's content field, i.e. the ColumnVector, in the form of four MACROS conveniently defined in the header file, and that is what I needed to work with first and foremost to get the example running.

<img src = "https://i.postimg.cc/mDcvNKsJ/Screenshot-from-2022-07-22-01-49-34.png"/>

  - The first field, `NV_CONTENT_C` is used everytime the ColumnVector has to be accessed from the code to apply a specific operation on it. Since the content fild is actally a void pointer, with no associated data type, we have typecasted it here into a ColumnVector.
  - `NV_LENGTH_C` uses the numel() function of the ColumnVector class to return the exact number of elements in the vector, often used for element-wise operations throughout the code, and `NV_DATA_C` provides a way to access the pointer to the actual block of memory allocated for data to be stored in this ColumnVector.``
  - The final `NV_Ith_C` macro allows any element in the vector to be accessed using its index, once again proving to be very useful for element-wise operations.

Using these MACROS, and quite some debugging to make sure only required functions were implemented, as some of the optional ones don't work as intended, I was able to get the [idaHeat2Dkry.c](https://github.com/LLNL/sundials/blob/main/examples/ida/serial/idaHeat2D_kry.c) example working with my [custom implementation](https://github.com/sarrah-basta/octave/commit/2a7846b454089f4c4a73c1138efc40c910bd9cb7) of the N_Vector. However, this was just temporary, as optional functions not used here could be required by IDALS or even the matrix / linear solver implementations. One such instance was the N_VSetArrayPointer(), which didn't work as intended as our MACRO returned a data member private to a class, and it wasn't possible to write to it. This will be tackled later in the project.

- **C or C++** <br>
One hitch throughout this project was that SUNDIALS is entirely built using the C language, but Octave compiles oct-files using C++, and this was to be combined now as we were making custom implementations.

Thankfully, all of Sundials has been written with compatibility to be linked to C++, with the `extern "C"`
keyword being used to prevent name-mangling by the C++ compiler. Hence, I wrote the files for my custom implementations as well as the example, whih would eventually be replaced by the `__ode15__`file, in C++, once again taking care to wrap them with the `extern "C"` keyword, and was able to successfully make it work.

- **Datatypes and typecasting**
Of course, N_Vector had other data types such as sunindextype and realtype(configured to double during build) native to SUNDIALS as well, and similarly the ColumnVector class had a few functions dealing with data types like octave_idx_type and double, respectively. So far, since int for the indices and double matched respectively, the automatic typecasting is working well. 
However, the realtype being configured to double at build time is a limitation we wanted to remove, and one way to do it in the latter part of the project could beto check at configure time how Sundials is configured and then set a typename that could be used throughout the implementation denoting double or float as required.

# Week 5 : Just Keep Coding

- **The Next Custom Implementation : Sparse Matrix**
As the code was working with N_Vector, and I had almost figured out the way to get it working easily, I straightaway tried the next custom implementation, to replace SUNMatrixSparse, using octave's own Sparse Matrix class.

The drill remained pretty the same, identify the [class](https://docs.octave.org/doxygen/8/d6/d2c/classSparseMatrix.html) to be used in octave, link the correct libraries, find the relevant functions to implement the MACROS, and test the code example.
<img src="https://i.postimg.cc/Wzq5hJZJ/Screenshot-from-2022-07-22-03-29-00.png"/>

- There were just two things different this time:
  - The SUNDIALS implementation supported both CSC and CSR representations, however, since the both the solver and Octave's own class only used the CSC format, I too modeled my implementation and MACROS accordingly.  
  - The example we had been using so far, had to be changed.

- **Switching up the example**
The example being used earlier, idaHeat2Dkry.c was a simple equation that did not require the use of the Sparse Matrix. So I had to go back to the base example most similar to Ode15, [idaHeat2Dklu.c](https://github.com/LLNL/sundials/blob/main/examples/ida/serial/idaHeat2D_klu.c). This required me to also link the klu library correctly with it, which changed the Makefile as well, but it worked suuccessfully.

- **Integrating into Octave's source tree**
Since both the implementations were somewhat working, I thought of adding both of them to the Octave source tree and testing them alongside Ode15. To this, I added all the 4 files, header and source for both the implementations, to the octave/libinterp/dldfcn , i.e the same path where `__ode15__.cc` resides, and added them to the `module.mk` file so they could be included in the Makefile auto-generated by Autotools.
Here, I found out that the N_VSetArrayFunction is required and it not working is causing the entire solver to collapse, so I'll be working on getting that fixed.

- **Is just working code enough ?**
Unfortunately the answer is No. Yes my wrappers for custom implementations are working, but because I have reused most of SUNDIALS basic serial implementation code, there  are a lot of optimization possibilities that are currently missing. 
The current implementations are not actually wrappers to ColumnVector methods, but rather elementwise operations on ColumnVector entries, and this is bad because in the actual methods used by the ColumnVector class methods and operators, the code is vectorized, so it avoids for loops and uses whole-array operations. But wwhat benefir does this have ?
From Octave's own documentation :
`In vectorization, Octave delegates this operation to an underlying implementation which, among other optimizations, may use special vector hardware instructions or could conceivably even perform the additions in parallel.  In general, if the code is vectorized, the underlying implementation has more freedom about the assumptions it can make in order to achieve faster execution.`

Hence this part is crucial, and I will be spending the next 1-2 weeks going over the ridiculously large amount of unoptimized code, and try to make it better with wrappers to actual ColumnVector functions and get these custom implementations right.