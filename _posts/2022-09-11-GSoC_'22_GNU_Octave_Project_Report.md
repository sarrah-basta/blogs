---
layout: post
title: "GSoC '22 @ GNU Octave : Project Report"
subtitle: "From First Steps to the Final Results "
date: 2022-09-11
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# ode15{i,s} : Matlab Compatible DAE solvers

## Important Links for the project**

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
    <th class="tg-0pky">Topic<br></th>
    <th class="tg-0pky">Links</th>
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

## The Project from a bird's eye view

My project was [Ode15{i,s}  : Matlab Compatible Solvers](https://summerofcode.withgoogle.com/programs/2022/projects/fg4hsOK0), and as the name suggests revolved around two specific solvers from the [set provided by Octave](https://docs.octave.org/v4.4.0/Matlab_002dcompatible-solvers.html) for initial value problems for Ordinary Differential Equations that have a MATLAB-compatible interface. My project was a continuation to the initial implementation of these during [GSoC '16](https://summerofcode.withgoogle.com/archive/2016/projects/6106026155704320), and focused on improving the current state of the solvers, heavily involved with the [SUNDIALS Library](https://computing.llnl.gov/projects/sundials) used in the backend. <br>

### Need for the project
The main problem to be solved stemmed from the fact that [SUNDIALS IDA](https://sundials.readthedocs.io/en/v6.2.0/ida/index.html) made use of the [KLU Library](https://github.com/DrTimothyAldenDavis/SuiteSparse/tree/master/KLU) for **sparse computations**. Any issue in linking this with respect to SUNDIALS, so as to generate the SUNLINSOL_KLU Linear Solver Interface, which packaged versions of SUNDIALS are often not compiled with (yes, even for widely popular operating systems such as [Ubuntu 20.04](https://octave.discourse.group/t/help-for-build-octave-7-1-0-on-ubuntu-20-04-wsl2/2910/2#:~:text=Yes%2C%20I%20think,the%20case%20indeed.), this resulted in the entire computation being carried out using **dense Jacobian matrices** instead, causing the user huge hits in terms of performance ! <br>
Hence, this project effectively created an [interface to SUNDIALS IDA API, using Octave's own classes and solvers](https://computing.llnl.gov/projects/sundials/release-history#:~:text=Changes%20to%20SUNDIALS%20in%20major%20release%20v5.0.0), also simultaeneously taking advantage of [not having to create copies](https://wiki.octave.org/Projects#:~:text=Matlab%2Dcompatible%20ODE%20solvers%20in%20core%2DOctave) of the data in SUNDIALS as well as Octave's vectorized operations, creating a safety net for the end user such that sparse computations are **never disabled** in the Ode15{i,s} solvers.

## Effects of the project for the end user

*As is for most users that use a user interface, what happens in the backend, stays in the backend.*

Meaning, before the existence of my project, if the KLU library was not found, the user would merely recieve the warning <br>
`KLU library not found.  This will result in some lack of functionality for sparse matrices.` <br>
What this lack of functionality actually meant was, internally, even when the user entered a "sparse" Jacobian, it was stored and computed using a "dense representation", causing extreme performance degradation. <br><br>
Now, the warning has been replaced with <br>
`KLU library not found.  This will result in some lack of functionality for sparse matrices.
Sparse computations in ode15{i,s} solvers will still be supported but will suffer a hit in time taken.` <br>
Here, ode15{i,s} still supports "sparse computations", meaning the user-entered Jacobian is internally stored in a "CSC (Compressed Sparse Column)" representation, and solved using Octave's own [polymorphic sparse LU solver](https://docs.octave.org/v4.4.1/Sparse-Linear-Algebra.html), instead of KLU. <br><br>

So, to what extent does this help in the performance degradation taking place earlier? 

- **Results for a PDE with variable NDOFS (3D Heat Equation)** <br>
*The test file used can be found [here](https://octave.discourse.group/t/gsoc-2022-project-ode15-i-s/1343/154?u=sarrah-basta)*



```
N = 100 ndof = 64
Ode15i with KLU Solvers : Elapsed time is 0.0322931 seconds.
Ode15i with Octave's solvers : Elapsed time is 0.0424781 seconds.
Ode15i with full impletentation : Elapsed time is 0.0676968 seconds..

N = 500 ndof = 343
Ode15i with KLU Solvers : Elapsed time is 0.0539529 seconds.
Ode15i with Octave's solvers : Elapsed time is 0.124162 seconds.
Ode15i with full impletentation : Elapsed time is 0.389471 seconds.

N = 1000 ndof = 729
Ode15i with KLU Solvers : Elapsed time is 0.183311 seconds.
Ode15i with Octave's solvers : Elapsed time is 1.42844 seconds.
Ode15i with dense implementation : Elapsed time is 3.63896 seconds.

N = 5000 ndof = 4913
Ode15i with KLU Solvers : Elapsed time is 8.73904 seconds.
Ode15i with Octave's solvers : Elapsed time is 120.542 seconds.
Ode15i with full impletentation : Elapsed time is 449.841 seconds. 

N = 10000 ndof = 9261
Ode15i with KLU Solvers : Elapsed time is 62.2595 seconds.
Ode15i with Octave's solvers : Elapsed time is 182.939 seconds.
Ode15i with full impletentation : Elapsed time is 2189.79 seconds.
```
These results show that while KLU is still better, our implementation of using the Sparse LU solver is hugely beneficial when KLU is not found, preventing us falling to the dense implementation.

- **Remaining Tasks** <br>

The skeletal code is complete, such that any user could use this by merely running configure and make, and it also passes all the previous tests in the ode15{i,s} m-files with the new implementations, keeping KLU optional and using it when required. There, are a few things that would be bettered, such as ensuring there are no memory leaks and clean garbage collection, improving the speed of the solver itself with profiling and optimization techniques, and a few FIXMEs (such as [this](https://github.com/sarrah-basta/octave/blob/3e682cb20b9935d39b647209b53b7b0347abefa0/libinterp/dldfcn/oct-sundials.h#L53-L59) and [this](https://github.com/sarrah-basta/octave/blob/3e682cb20b9935d39b647209b53b7b0347abefa0/libinterp/dldfcn/oct-sundials.cc#L570-L573), after which the code could be merged.

- **Build System** <br>

The build system has minimal changes to what it was before, the majority of it being comprised in [these lines](https://github.com/sarrah-basta/octave/blob/3e682cb20b9935d39b647209b53b7b0347abefa0/libinterp/dldfcn/oct-sundials.cc#L570-L573)
I have used a conditional methodology I have used to build the `oct-sundials.cc` as a dependency of ode15, which is the only place it will be used.



- **Consolidation of work done** <br>
 I have consolidated all the work done in my GSoC project into 1 patch posted on the Savannah patch tracker used by GNU Octave at this link [ADDD PATCH TRACKER LINK](), which is analogous to [this commit](https://github.com/sarrah-basta/octave/commit/3e682cb20b9935d39b647209b53b7b0347abefa0).
