---
layout: post
title: "Introducing my GSoC Project at GNU Octave"
subtitle: "Starting from the Source Code."
date: 2022-06-10
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# Ode15{i,s} : Matlab Compatible Ode Solvers

My Project at [GNU Octave](https://wiki.octave.org/GNU_Octave_Wiki) is called [Ode15{i,s} : Matlab Compatible Ode Solvers](https://summerofcode.withgoogle.com/programs/2022/projects/fg4hsOK0) under Google Summer of Code '22. This project involves improving the implemented solvers with more efficient algorithms for helper functions, interpolation and extended support for data types, precision and Octave’s own data structures and classes for use by Sundial’s solvers, whilst staying true to Octave’s claim of providing its users with a Matlab compatible interface.

The approach to fulfil this is divided into 3 major segments :
- Consistent Initial Conditions
- Using Octave's own Data Structures and Linear Solvers in Computation <br>
and 
- Improving existing implementation with missing functions.

## Community bonding Period : A small start

Working with huge codebases alongside multiple contributors is surely a daunting task, and GSoC not only understands it, but provides a way to make it achievable. 

Here is how I used the all important time-period between knowing I was selected as a contributor and before the 12-week coding period actually starts in setting up the stepping stones for my project :

1. <u>Studying the [research paper for consistent initial conditions](https://www.degruyter.com/document/doi/10.1515/JNMA.2002.291/html)</u> <br>
I had originally referred to this paper during my proposal to improve the implementation and revisited it along with preparing a small pseudocode in order to discuss it with my mentor.

2. <u>Understanding Mercurial Worlflows</u> <br> 
My organisation, GNU Octave uses the Mercurial Source Control Tool, a workflow I a not all too familiar with. Suring the application process I had tried contributing two patches to fix a few bugs in Octave, and later realised they weren't as clean as they were supposed to be, and had to redo them. (`Psst`, so that no one else makes the same mistakes I did, here's a handy [cheat sheet](https://github.com/sympy/sympy/wiki/Git-hg-rosetta-stone) I found)

3. <u>The Evolution of Sundials</u><br>
No, not the kind used to look at time, those have long since evolved into modern day clocks. I am talking about the [SUNDIALS](https://computing.llnl.gov/projects/sundials) library, which would be monumental to my project in the near future, coming with their first major release in two years, `v6.0.0`.
Earlier when I had built [Octave from source](https://wiki.octave.org/Building), I had used the handy [one-liner](https://wiki.octave.org/Octave_for_Debian_systems#The_right_way) to install its dependencies on my Unix-based Ubuntu system. However, this ended up with me downloading SUNDIALS v5.7.0, default packaged in libsundials-dev. However, the new release introduced quite some new things, such as the `SUNContext object on which all other SUNDIALS objects depend`, additional capacaties for profiling, memory help, etc which meant I would have to now build SUNDIALS and all its dependencies such as KLU, from source.

And just like that, the leisure period is over, and it is time for the actual work to begin!

