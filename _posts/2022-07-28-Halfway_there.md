---
layout: post
title: "GSoC Progress Report : Week 6"
subtitle: "Halfway There"
date: 2022-07-28
background: '/img/posts/gsococtave.png'
tags: gsoc
---

# Week 6 : Reuse, Optimize and Vectorize
<br>

- **Reproducibility** <br>
Firstly, I tried to make sure the code I was writing so far and commiting to my github could be reproduced and checked by anyone. So now, the complete status and instructions for how the code written by me can be reproduced and tested on various systems is documented in this [README](https://github.com/sarrah-basta/octave/tree/no_klu#:~:text=Code%20in%20The%20No%2DKLU%20Directory) file on my branch, and I will keep updating it as per any changes in subsequent commits. 

- **Optimization** <br>
  While the NVector implementation can be used in the example shown, I [worked on](https://github.com/sarrah-basta/octave/commit/2e2c2d760969dfd3a72c9d24fd9f9a89bad652ed#diff-a3d11327306987686626dcc14f7be068448172fa0825f72614fc6102c5e5cec9) it throughout this week function-by-function to make them [wrappers to ColumnVector methods](https://github.com/sarrah-basta/octave/commit/db668918125cf12446863da206fbf2cb4201029f#diff-77cc9dd91dc45ccce2e506f83099d81d2f672f369ad1701f6d22e0a655d35cb5) rather than elementwise operations on ColumnVector entries, to take advantage of vectorization in octave.
  [/quote]

  Here, I first [identified only the required functions needed by IDA](https://github.com/sarrah-basta/octave/commit/030a769ef21c370d6f1b38e65833d325e1eeed0c) , [added the tests](https://github.com/sarrah-basta/octave/commit/c720c8d30c284c1b79102496af0a45632d131a9e) I was using on my local system to test each function individually, [documented the tests and code](https://github.com/sarrah-basta/octave/commit/68bb1357a8cc33b3df4d9e58e68d818cca2dd436) to be reproducible on other systems, learnt about overloaded operators by broadcasting and using [builtin functions](https://github.com/sarrah-basta/octave/commit/0cb50d2a091704d3fa98cb0e5e7830c670e0a165) and have since then been 
  trying to replace all required functions by IDA with the correct ColumnVector function implementations, in the N_Vector implementation. Here are the functions I have worked on since then, so you can view the Octave functions I have used in my wrappers at a glance : 

  - [Basic Functions](https://github.com/sarrah-basta/octave/commit/db668918125cf12446863da206fbf2cb4201029f#diff-77cc9dd91dc45ccce2e506f83099d81d2f672f369ad1701f6d22e0a655d35cb5) upto [N_VProd and N_VDiv](https://github.com/sarrah-basta/octave/commit/2e2c2d760969dfd3a72c9d24fd9f9a89bad652ed)
  - N_VScale, N_VConstrMask and [N_VCompare](https://github.com/sarrah-basta/octave/commit/030a769ef21c370d6f1b38e65833d325e1eeed0c#diff-a3d11327306987686626dcc14f7be068448172fa0825f72614fc6102c5e5cec9R834-R850) (small problems that I have been unable to solve in their regard)
  - [N_VInvTest and N_VWrmsNorm](https://github.com/sarrah-basta/octave/commit/8988cdd523e6ce2c6bba89b5eabcb0ceefdb19a0)
  - [N_VDotProd and N_VMaxNorm](https://github.com/sarrah-basta/octave/commit/f0eb3b8b8077ef6c2458c57f97637e2fcfdee773)
  - [N_VWrmsNormMask and N_VMinQuotient](https://github.com/sarrah-basta/octave/commit/7d6aa80989628959fc29b276d1769d6eac784014)
  - [N_VLinearCombination and N_VScaleAddMulti](https://github.com/sarrah-basta/octave/commit/eda7701929f0264f0afdf06adad5282da4210049)
  - [N_VLinearSumVectorArray and N_VScaleVector](https://github.com/sarrah-basta/octave/commit/4576f2520fce9cd90dfab31da154f8fc25615b4f)
  and 
  - made sure all of these work with the [2DHeatKry example](https://github.com/sarrah-basta/octave/blob/no_klu/no-klu/src/kry_example.c) and pass [all tests](https://github.com/sarrah-basta/octave/blob/no_klu/no-klu/src/test_nvector_octave.cpp).

In everything mentioned above, I have tried to best of my ability to use as many optimized functions and test so as to ensure this custom implementation of N_Vector would work as intended. I just have a few small things I would like to confirm wrt functions N_VScale, N_VCompare and N_VConstrMask, to make sure the way I have approached them is correct. 

- **Goals for next week** <br>
  I believe according to our discussions the next steps would be : 
  - Making the SUNMatrix Custom Implementation also work with [wrappers to functions instead of entries](https://github.com/sarrah-basta/octave/blob/42ed2792119d4225341c6d8ca81e8b093225834e/no-klu/src/sunmatrix_oct.h#L80)
  - The following [four functions only](https://sundials.readthedocs.io/en/latest/sunmatrix/SUNMatrix_package_links.html#sunmatrix-functions-used-by-ida) would have to be changed if we are to work with our own Linear Solver.
  - Deciding on an example to test the SUNMatrix implementation and if we should test it with another solver such as KLU or first make our own custom implementation of the Linear Solver
  - Discussion on which linear solver from octave should be used