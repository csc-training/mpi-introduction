## Heat equation solver in parallel with MPI

Parallelise our implementation of a two-dimensional heat equation solver using
MPI. See [Code description](code-description.md) for some theory and more
details about the code.

To parallelise the code, one needs to divide the grid into blocks of columns
(in Fortran) or rows (in C) and assign each block to one MPI task. Or in other
words, share the work among the MPI tasks by doing a domain decomposition.

![2D domain decomposition](img/domain-decomposition.svg)

The MPI tasks are able to update the grid independently everywhere else than
on the boundaries -- there the communication of a single column (or row) with
the nearest neighbour is needed. This can be achieved by having additional
ghost-layers that contain the boundary data of the neighbouring tasks. As the
system is aperiodic, the outermost ranks communicate with only one neighbour,
and the inner ranks with two neighbours.

Implement this "halo exchange" operation in the `exchange()` routine in
[c/core.c](c/core.c) or [fortran/core.F90](fortran/core.F90). You will also
need to initalise and finalise MPI at suitable positions in
[c/main.c](c/main.c) or [fortran/main.F90](fortran/main.F90).

To build the code, please use the provided `Makefile` (by typing `make`).
