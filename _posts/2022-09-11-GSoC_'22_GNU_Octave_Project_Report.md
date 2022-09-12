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

## Important Links 
The discussion for the code and changes lives at : [Submission via Patch Tracker]() <br>
The discussion with the community lives at : [Discourse Topic - Ode15{i,s} Project Page ](https://octave.discourse.group/t/gsoc-2022-project-ode15-i-s/1343?u=sarrah-basta) <br>
The repository worked on during the project is maintained at : [Octave Github Mirror](https://github.com/sarrah-basta/octave.git) <br>

## The Project from a bird's eye view

  My project was [Ode15{i,s}  : Matlab Compatible Solvers](https://summerofcode.withgoogle.com/programs/2022/projects/fg4hsOK0), and as the name suggests revolved around two specific solvers from the [set provided by Octave](https://docs.octave.org/v4.4.0/Matlab_002dcompatible-solvers.html) for initial value problems for Ordinary Differential Equations that have a MATLAB-compatible interface. My project was a continuation to the initial implementation of these during [GSoC '16](https://summerofcode.withgoogle.com/archive/2016/projects/6106026155704320), and focused on improving the current state of the solvers, heavily involved with the [SUNDIALS Library](https://computing.llnl.gov/projects/sundials) used in the backend. <br>


- **Final Results** <br>

Along with using Octave's implementations for Sparse Solvers, I added support for the dense implementations, and I could now test against the full implementation using SUNDIALS Dense Solver as well ! Here are the final results :

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
