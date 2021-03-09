**Problem 1**

Original runtime: 6.087

Two methods of vectorization using scipy.convolute, one with only one for-loop for time, the other using a recursive function.

for-loop runtime: 0.1442

recursive function runtime: 0.1420


**Problem 2**

Ran compiler optimizations: jupyter-notebook -mpcu=cortex-a72 -mfpu=neon-fp-armv8

Original runtime with optimization: 4.7327

for-loop runtime with optimization: 0.1357

recursive function runtime with optimization: 0.115975

Tryed but abandonded: cpython, overclocking.



