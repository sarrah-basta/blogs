---
layout: post
title: "GSoC '22 @ GNU Octave : Project Report"
subtitle: "From First Steps to the Final Results "
date: 2022-09-11
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# ode15{i,s} : Matlab Compatible DAE solvers
<br>

### Important Links for the project

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky"><b>Topic</b><br></th>
    <th class="tg-0pky"><b>Links</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky">Code and Changes</td>
    <td class="tg-0pky"><a href="https://savannah.gnu.org/patch/?10278"><span style="color:#905">Submission via Patch Tracker</span></a></td>
  </tr>
  <tr>
    <td class="tg-0pky">Community and Discussion</td>
    <td class="tg-0pky"><a href="https://octave.discourse.group/t/gsoc-2022-project-ode15-i-s/1343?u=sarrah-basta"><span style="color:#905">Discourse Topic - Ode15{i,s} Project Page </span></a></td>
  </tr>
  <tr>
    <td class="tg-0pky">Repository</td>
    <td class="tg-0pky"><a href="https://github.com/sarrah-basta/octave.git"><span style="color:#905">Octave Github Mirror</span></a></td>
  </tr>
</tbody>
</table>
<br>

##  A bird's eye overview


My project was [Ode15{i,s}  : Matlab Compatible Solvers](https://summerofcode.withgoogle.com/programs/2022/projects/fg4hsOK0), and as the name suggests revolved around two specific solvers from the [set provided by Octave](https://docs.octave.org/v4.4.0/Matlab_002dcompatible-solvers.html) for initial value problems for Ordinary Differential Equations that have a MATLAB-compatible interface. My project was a continuation to the initial implementation of these during [GSoC '16](https://summerofcode.withgoogle.com/archive/2016/projects/6106026155704320), and focused on improving the current state of the solvers, heavily involved with the [SUNDIALS Library](https://computing.llnl.gov/projects/sundials) used in the backend. <br>

#### Need for the project
The main problem to be solved stemmed from the fact that [SUNDIALS IDA](https://sundials.readthedocs.io/en/v6.2.0/ida/index.html) made use of the [KLU Library](https://github.com/DrTimothyAldenDavis/SuiteSparse/tree/master/KLU) for **sparse computations**. Any issue in linking this with respect to SUNDIALS, so as to generate the SUNLINSOL_KLU Linear Solver Interface, which packaged versions of SUNDIALS are often not compiled with (yes, even for widely popular operating systems such as [Ubuntu 20.04](https://octave.discourse.group/t/help-for-build-octave-7-1-0-on-ubuntu-20-04-wsl2/2910/2#:~:text=Yes%2C%20I%20think,the%20case%20indeed.)), this resulted in the entire computation being carried out using **dense Jacobian matrices** instead, causing the user huge hits in terms of performance ! <br>
Hence, this project effectively created an [interface to SUNDIALS IDA API, using Octave's own classes and solvers](https://computing.llnl.gov/projects/sundials/release-history#:~:text=Changes%20to%20SUNDIALS%20in%20major%20release%20v5.0.0), also simultaeneously taking advantage of [not having to create copies](https://wiki.octave.org/Projects#:~:text=Matlab%2Dcompatible%20ODE%20solvers%20in%20core%2DOctave) of the data in SUNDIALS as well as Octave's vectorized operations, creating a safety net for the end user such that sparse computations are **never disabled** in the Ode15{i,s} solvers.

## Effects of the project for the end user

> *As is for most users that use a user interface, what happens in the backend, stays in the backend.*

Meaning, before the existence of my project, if the KLU library was not found, the user would merely recieve the warning <br>
`KLU library not found.  This will result in some lack of functionality for sparse matrices.` <br>
What this lack of functionality actually meant was, internally, even when the user entered a "sparse" Jacobian, it was stored and computed using a "dense representation", causing extreme performance degradation. <br><br>
Now, the warning has been replaced with <br>
`KLU library not found.  This will result in some lack of functionality for sparse matrices.
Sparse computations in ode15{i,s} solvers will still be supported but will suffer a hit in time taken.` <br>
Here, ode15{i,s} still supports "sparse computations", meaning the user-entered Jacobian is internally stored in a "CSC (Compressed Sparse Column)" representation, and solved using Octave's own [polymorphic sparse LU solver](https://docs.octave.org/v4.4.1/Sparse-Linear-Algebra.html), instead of KLU. <br>

> So, to what extent does this help in the performance degradation taking place earlier? 

**Solving a PDE with variable NDOFS (3D Heat Equation) using Ode15i** <br>

>>> The test file used can be found [`here`](https://octave.discourse.group/t/gsoc-2022-project-ode15-i-s/1343/154?u=sarrah-basta)

> *`KLU Solvers` indicates SUNDIALS IDA using the [SUNLinsol_KLU](https://sundials.readthedocs.io/en/latest/sunlinsol/SUNLinSol_links.html#the-sunlinsol-klu-module) module as the linear solver, which is an interface to [Suitesparse/KLU](https://people.engr.tamu.edu/davis/suitesparse.html)* <br>
______________________________________________________________________________________________________<br>
> *`Octave Solvers`  indicates SUNDIALS IDA using my custom-made [OCTLinSol_Gen](https://github.com/sarrah-basta/octave/blob/6b06ba569728d9a705d464f91ed20e421ff75cee/libinterp/dldfcn/oct-sundials.cc#L1041) module as the linear solver, an interface to Octave's polymorphic solver specified above, which is now used for sparse computation if KLU is not found.* <br>
______________________________________________________________________________________________________<br>
> *`Dense Solvers`  indicates SUNDIALS IDA using the [SUNLinSol_Dense](https://sundials.readthedocs.io/en/latest/sunlinsol/SUNLinSol_links.html#the-sunlinsol-dense-module) module as the linear solver, which was the default earlier if KLU was not found.* <br><br>

--------------------------------------------
```
N = 500 ndof = 343
KLU Solvers : Elapsed time is 0.0539529 seconds.
Octave's Solvers : Elapsed time is 0.124162 seconds.
Dense Solvers : Elapsed time is 0.389471 seconds.

N = 1000 ndof = 729
KLU Solvers : Elapsed time is 0.183311 seconds.
Octave's Solvers : Elapsed time is 1.42844 seconds.
Dense Solvers : Elapsed time is 3.63896 seconds.

N = 5000 ndof = 4913
KLU Solvers : Elapsed time is 8.73904 seconds.
Octave's Solvers : Elapsed time is 120.542 seconds.
Dense Solvers : Elapsed time is 449.841 seconds. 

N = 10,000 ndof = 9261
KLU Solvers : Elapsed time is 62.2595 seconds.
Octave's Solvers : Elapsed time is 182.939 seconds.
Dense Solvers : Elapsed time is 2189.79 seconds.
```
--------------------------------------------
These results show that while KLU is still better, and is hence the first choice, our implementation of using the Sparse LU solver is hugely beneficial when KLU is not found, preventing the solver from falling to the dense implementation.


## Work done throughout GSoC

* Learnt about creating [custom implementations of required data structures](https://sundials.readthedocs.io/en/latest/nvectors/NVector_API_link.html#implementing-a-custom-nvector) to interface with [SUNDIALS IDA API](https://sundials.readthedocs.io/en/latest/nvectors/NVector_package_links.html#nvector-functions-used-by-ida).

* Created custom implementations for - `NVector`, `SUNMatrix` and `SUNLinearSolver` as [wrappers](https://github.com/sarrah-basta/octave/blob/default/libinterp/dldfcn/oct-sundials.cc) to [corresponding classes](https://docs.octave.org/doxygen/8/d0/d25/classColumnVector.html#ad51f1d6fce69ed4c02c43713422789b4) -       `ColumnVector`, `SparseMatrix` and `SparseMatrix->solve()` in Octave. Added [function-wise tests](https://github.com/sarrah-basta/octave/blob/25b1cbc126e767bd658419b35449af5d1a8109de/README.md#code-in-the-no-klu-directory) to test each implementation individually, and successfully tested interface with IDA API via the [IDA2DHeatKry](https://github.com/LLNL/sundials/blob/1375f0563ef953e0972277a0af71fc4388f5787a/examples/ida/serial/idaHeat2D_kry.c) example. 

> The custom implementations created have now been consolidated and stored in Octave's file structure as <br>
> `libinterp/dldfcn/oct-sundials.h` <br>
> `libinterp/dldfcn/oct-sundials.cc` <br>

* Integrated custom implementations and tested the performance of interface to Octave's Solver against KLU in Ode15i. Integrated with current build system that uses [GNU Autotools](https://www.gnu.org/software/automake/manual/html_node/GNU-Build-System.html), compiling `__ode15__.cc` oct-file linked against custom implementations.

* On obtaining the shown results and deciding to use Octave's Solvers as default only when KLU is not found, made use of [particular header check macros](https://www.gnu.org/software/autoconf/manual/autoconf-2.67/html_node/Particular-Headers.html#Particular-Headers) set by [autoheader](https://www.gnu.org/savannah-checkouts/gnu/autoconf/manual/autoconf-2.71/html_node/autoheader-Invocation.html), to redesign the oct-file as follows :


--------------------------------------------
```
#if defined (HAVE_SUNDIALS)
  #include "oct-sundials.h"  
  /* containing NVector_Octave, OCTMatrix_Sparse 
     and OCTLinsol_Sparse */

if (user passes sparse jacobian)
{
  #if defined (HAVE_SUNDIALS_SUNLINSOL_KLU)
    use
    - NVector_Serial
    - SUNMatrix_Sparse
    and
    - SUNLinSol_KLU
  #else
    use
    - NVector_Octave
    - OCTMatrix_Sparse
    and
    - OCTLinSol_Sparse
  #endif
}
else (user has passed dense jacobian)
{
  use
    - NVector_Dense
    - SUNMatrix_Dense
    and
    - SUNLinSol_Dense
}
```
--------------------------------------------

* Updated warnings shown to user and tests in Ode15{i,s} m-files to reflect improvements made. 


### Future Improvements <br>

The `custom implementations and their interface to Ode15{i,s} solvers` is hence complete, such that any user could use this by merely running configure and make, and it also passes all the previous tests in the ode15{i,s} m-files with the new implementations, keeping KLU optional and using it when required.<br>
Some improvements that could be made are :
- Monitor memory usage using profiler tools and garbage collectors
- Further optimization in the custom implementations
- Redesigning macros set by autoconf
> For example, the #HAVE_SUNDIALS macro currently checks for a lot of Sundials interfaces, such as NVecSerial, which might not be needed if we are to just use the custom implementations, which only require basic Sundials files. Simpler macros code be made for this usecase. 

after which the code should prove to be a useful addition to the Matlab-Compatible ODE and DAE Solvers offered in Octave.

## Acknowledgements

> After my exhilirating journey of `GSoC @ GNU Octave` in the past few months, I am proud of a lot of things, but mostly of myself, for having been able to absorb even tiny bits from the vast knowledge of my mentors and peers, this priceless experience of each up, down, error and success in the lines of code written by me giving me much to look forward to in a future career in development, research and open-source.

I am grateful and extremely thankful to the entire community at GNU Octave, with everyone from experienced Octave developers to even fellow GSoC contributors never hesitant to contribute to every discussion, ranging from mathematical code at the heart of Octave, to build systems and debugging errors. I have carried out most of the discussion throughout my project at the [developer forum](https://octave.discourse.group/) and am truly amazed at the inclusivity and collaborative spirit of everyone here, making my first deep-dive into open-source an extremely cherished experience!
<br>

But most of all I would like to thank my mentor, [Carlo de Falco](https://it.linkedin.com/in/carlo-de-falco-656ba013) for his expert guidance and support. My over-ambitiousness and naivety of including too much in my intial proposal were met with calm advice to be accurate and even conservative when estimating the effort of tasks so as to be able to work more efficiently, my questions were met with patient and detailed explanations, not just of the answer at hand but also of how to be able to arrive at it myself the next time, and most importantly, my mistakes were met with concise criticism rather than reprimandation. <br>
Instead of hard-set deadlines to meet, I was encouraged and set achievable goals for myself, even being met with content when I redefined the timeline in the first few weeks of the project. This allowed me to truly explore, learn and develop a sense of accountability to complete *my* project, being allowed to learn myself about simple things such as name-mangling by C++ compilers or artificial polymorphism in C to the importance of vectorized operations and memory management (accompanied with a good many nights spent with GDB), and as a result I have improved my programming skills significantly since when I started, learning both theory and by practice ! <br>    

I hope to see this project to its completion and keep contributing to GNU Octave.



