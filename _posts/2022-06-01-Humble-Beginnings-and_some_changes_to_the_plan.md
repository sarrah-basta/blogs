---
layout: post
title: "Humble Beginnings ... and some changes to the plan"
subtitle: "A concise report of weeks 1-3 of my project in GSoC '22."
date: 2022-07-14
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# Week 1 : Getting Started 

I began with a slow start, and the first week went into some planning to get a clear idea of what lay ahead in this project, and the development setup that would be required for the same.

- **Communication throughout the project** <br>
It was agreed with my mentor that we would communicate about the progress via GNU Octave's [discourse](https://octave.discourse.group/), specifically this [topic](https://octave.discourse.group/t/gsoc-2022-project-ode15-i-s/1343) made especially for the project, along with occasional audio/video calls when required and weekly meetings to share progress with other memebers of the community.

- **Development Environment Setup**
    - Code Sharing <br>
For the initial stages of the project, the project would be developed in this [github repository](https://github.com/sarrah-basta/octave), a fork of the Git Mirror of Octave's source code and later on, once each independant element of the project would be fully working, it will be transitioned into core octave.

    - Versions and Libraries <br>
As pointed out in the previous post, SUNDIALS, a library that would be required throughout the project, was to be built from [source](https://github.com/LLNL/sundials/tree/main) on the machine used by me, not only to be able to use it's latest version, but also as the first parts of the project (the custom implementations) would be heavily reliant on reading the code in the library, as well as using some [test functions](https://github.com/LLNL/sundials/blob/main/examples/nvector/test_nvector.c) provided alongside it. I configured the Sundials library and installed it as per instructions, making sure it was linked with the KLU library, and then setup the Octave build to use both of them. <br>
For **faster building times**, as I would be working hugely with .cc and oct files in octave which would require to run the `make` command frequently, I set up an octave build without GUI and other optional features that I would not reuire for testing my project as follows :

`../octave/configure --prefix=/usr --without-qt --without-x --disable-java --without-sndfile --without-portaudio --without-magick --without-fltk`

- **Start of the timeline**<br>
    - On the guidance of my mentor, we agreed to lowering the priority of the bettwr implementation of consisitent initial conditions in Ode15{i,s}, namely the `decic` function.<br>
    - Instead we decicded to kick of the project with the idea of custom implementations of the NVector, SUNMatrix data structures and KLU solver, used by the *IDA (Implicit Differential-Algebraic)* solver in SUNDIALS, using similar data structures from Octave, and eventually, create a wrapper for currently implementations to use Octave's own, more optimized methods and operators. <br>

In conclusion, the first task for this, set to be completed by the next two weeks, would be to adapt this [IDA Example](https://github.com/LLNL/sundials/blob/main/examples/ida/serial/idaHeat2D_klu.c), to work with liboctave classes.

# Week 2 : It's Documentation time !

```
It's harder to read code than to write it
                                         - Joel Spolsky
```
But thankfully, for me, the SUNDIALS [documentation](https://sundials.readthedocs.io/en/latest/index.html) made understanding the code to create [custom implementations] a little easier.  

### Custom Implementation of N_Vector

The first step towards writing the custom implementation of NVector (native to SUNDIALS) using ColumnVector (from octave) would be isolating and understanding the exact methods that would need to be implemented, as not all are compulsory for its functionality.

- **NVector functions required by IDA**
<img src = "https://gcdnb.pbrd.co/images/CtRBZ1bPosSA.jpg" />

As mentioned IDA would be used both by our example as well as the ODE Solvers, so it's safe to say all implementations required by t would have to be brought to life. IDALS, however, has some optional implementations, but most of them are used in the Ode15{i,s} solver even if not in the example, hence they too would have to be implemented. <br> <br>

Aprt from these, SUNLinsol inerfaces of linear solvers in SUNDIALS also had their own requirements of vector operations to be implemented, such as : <br>
- The SPGMR solver (used in the example) <br> <br>
```
    /* check that the supplied N_Vector supports all requisite operations */
 if ( (y->ops->nvclone == NULL) || (y->ops->nvdestroy == NULL) ||
      (y->ops->nvlinearsum == NULL) || (y->ops->nvconst == NULL) ||
      (y->ops->nvprod == NULL) || (y->ops->nvdiv == NULL) ||
      (y->ops->nvscale == NULL) || (y->ops->nvdotprod == NULL) )
   return(NULL);
```  
- or the KLU Solver (used by Ode15) <br> <br>
```
  if ( (N_VGetVectorID(y) != SUNDIALS_NVEC_SERIAL) &&
       (N_VGetVectorID(y) != SUNDIALS_NVEC_OPENMP) &&
       (N_VGetVectorID(y) != SUNDIALS_NVEC_PTHREADS) )
    return(NULL);
```
which tries to make sure the N_Vector provided is one of the above implementations compulsorily. Temporarily to test our implementation against it, even though we don't plan to continue using it, I returned the identifier of our N_Vector as that of Serial N_Vector instead of `SUNDIALS_NVEC_CUSTOM`, and also will be keeping the serial nvector as a skeleton base with testing alongside it.

- **Linking, Building and Makefiles**

The first step to get the example running with liboctave classes, was well, getting the liboctave classes themselves, Breakthrough, right ! But, coming to the point, while the custom implementations were still in the development stage,I decided to first build the example isolated from both SUNDIALS and Octave's source trees, using only the libraries any user who has installed both softwares would have.

Thus, I ended up creating a [Makefile](https://github.com/sarrah-basta/octave/commit/2a7846b454089f4c4a73c1138efc40c910bd9cb7#diff-49e723e1819dfb94859a008084bca4c512cb8de8f3ec0d8805dc5c55c4d52e0e), in which when just the following important paths were set corrctly, anyone would be able to make the example work : <br>
```
# include+lib paths to be defined by the user temporarily
SUN_INC_PATH = /usr/local/include
OCT_INC_PATH = /usr/include/octave-8.0.0/octave

SUN_LIB_PATH = /usr/local/lib
OCT_LIB_PATH = /usr/lib/octave/8.0.0
LDFLAGS =-I$(SUN_INC_PATH) -I$(OCT_INC_PATH) -L$(SUN_LIB_PATH) -L$(OCT_LIB_PATH)

LIBS = -lsundials_generic -lsundials_nvecserial -lsundials_ida -lsundials_sunlinsolspgmr -loctave
```
<br> 

Of course, the *temporarily* is here as later, once merged into the Octave source tree, autotools would be able to find these paths itself :)

# Week 3 : Finally getting into the code !

#### The What
With everything set up and linked, it was now time to start creating the custom N_Vector implementation, hereon referenced as NVector_Col. Initially, the representations of NVector I was referring to and the [documentation](https://sundials.readthedocs.io/en/latest/nvectors/NVector_API_link.html#implementing-a-custom-nvector) provides quite some information about the operations of the vector, the data structure itself, essentially the *content* field talked about here, misled me a bit, and I wasn't sure how exactly a ColumnVector could be used instead of it, while still being accessed by operations in the generic NVector class... sounds confusing already doesn't it?

#### The Why
I did overcomplicate it a little for myself as well, but before I tell you how it's deceptively simple, why exactly did we want to replace the ColumnVector data structure from octave into code that was clearly working in this N_Vector? 
Well, because, the current code requires quite some work to make it work. In the current `__ode15__.cc` file, used for the Ode15{i,s} solvers, explicit functions such as `ColumnVector NVecToCol` and 
```
N_Vector
  IDA::ColToNVec (const ColumnVector& data, octave_f77_int_type n)
  {
    N_Vector v = N_VNew_Serial (n OCTAVE_SUNCONTEXT);

    realtype *punt = nv_data_s (v);

    for (octave_f77_int_type i = 0; i < n; i++)
      punt[i] = data(i);

    return v;
  }
```
are required, and it's obvious that those for loops and transferring the data costs the optimization a good amount of time and memeory, which we want to eliminate.

#### And The How
With the help of my mentor, I was able to realise where I was going wrong in my understanding (quite a bit contributed by the fact that I am much more used with C++ code, and SUNDIALS is written completely, but with some very clever designs in the way).

So here is how it works :

<img src = "https://i.postimg.cc/FR2XSYbS/flowchart.png" width = 800/>

And in this complicated diagram, all the details on how to navigate around the functions and ops structure are to be used as per the official documentation, and just the creating of the data structure by the constructor which ideally looks like 

`content = (type_of_content_structure) malloc(sizeof *content);` <br>
should be replaced with <br>
`content = new ColumnVector(length);` <br>

and then, all the following operations can be set accessing and/or storing data from this ColumnVector we have declared here!

In the coming weeks, I will try implementing those operations, which would require studying the octave::ColumnVector as well, solving any more problems that may arise along the way, and giving more shape to this project !