---
title: "Python - Elastic Wave Simulations"
date: 2021-9-3
tags: [Python, Finite Difference, FDM, Simulations]
excerpt: "A Python implementation of 2-Dimensional Elastic Wave Simulations"
mathjax: "true"
---

## Summary

While working on my Master's Thesis research, I needed an elastic wave simulation that I could automate and view the results programmatically. Industry solutions like COMSOL have great simulation capabilities, but are difficult to interface with - so I wrote this elastic wave simulation in Python. It uses Numba's JIT compiler to accelerate the calculations, MatPlotLib to generate the animations, and NumPY for inline array operations.

While the Finite Difference Time Domain method has a number of weaknesses when compared to methods like the Discontinuous Galerkin used by COMSOL, it's faster to formulate, and, most important for this case, fast to solve. This allows for rapid prototyping in signal processing, signal analysis, and my initial use for the application, time reversal. Time reversal requires an initial simulation and a second simulation using the results of the first - this is difficult in other software packages, as the data has to be exported, manipulated, and re-ran as the second simulation. This package allows for the immediate simulation of both sets of data, greatly increasing usability.

The code was accelerated using Numba's JIT compiler, which converts regular Python or NumPY instructions into high-performance compiled code - this code can also be run in parallel. This parallel optimization decreased runtime from 25ms per iteration to only 5 ms per iteration on a benchmark simulation. 

<img src="{{ site.url }}{{ site.baseurl }}/images/FDTD/Img1.gif" alt="Animation of elastic wave results">

This animation shows the propagation of waves within the material. The waves are generated as a sinusoid transmitted for two periods in the top-left of the animation. After some time, the waves intersect a different material in the middle of the image - this causes diffraction and reflection visible in the waves. Also visible are the different types of elastic waves - longitudinal waves quickly gain distance, while the shear waves - with a slower propagation speed - lag behind. 