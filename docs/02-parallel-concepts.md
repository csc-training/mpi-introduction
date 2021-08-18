---
title: Parallel computing concepts
author: CSC Training
date:   2020
lang:   en
---


# Computing in parallel

- Parallel computing
    - A problem is split into smaller subtasks
    - Multiple subtasks are processed simultaneously using multiple cores

 ![](img/compp.svg){.center width=40%}

# Types of parallel problems

- Tightly coupled
    - Lots of interaction between subtasks
    - Weather simulation
    - Low latency, high speed interconnect is essential
- Embarrassingly parallel
    - Very little (or no) interaction between subtasks
    - Sequence alignment queries for multiple independent sequences in 
      bioinformatics


# Exposing parallelism

<div class=column>
- Data parallelism
    - Data is distributed across cores
    - Each core performs simultaneously (nearly) identical operations
      with different data 
    - Cores may need to interact with each other, e.g. exchange
      information about data on domain boundaries
</div>
<div class=column>

 ![](img/eparallel.svg){.center width=80%}

</div>

# Exposing parallelism

- Task farm (master / worker)

 ![](img/farm.svg){.center width=60%}

- Master sends tasks to workers and receives results
- There are normally more tasks than workers, and tasks are assigned
  dynamically 

# Exposing parallelism

- MapReduce is special task farm like approach suitable especially for
  large scale data analysis 
- Two types of workers
    - Map: given an input data, emit list of (key, value) pairs
    - Reduce: combine the values for a key
- User works only with serial code for Map and Reduce operations
- MapReduce framework takes care of parallelization and data distribution

# Parallel scaling

<div class=column>
- Strong parallel scaling
    - Constant problem size
    - Execution time decreases in proportion to the increase in the number of cores
- Weak parallel scaling
    - Increasing problem size
    - Execution time remains constant when number of cores increases
      in proportion to the problem size 
</div>
<div class=column>

 ![](img/scaling.png){.center width=80%}

</div>

# What limits parallel scaling

- Load imbalance
    - Distribution of workload to different cores varies
- Parallel overheads
    - Additional operations which are not present in serial calculation
    - Synchronization, redundant computations, communications
- Amdahl’s law: the fraction of non-parallelizable parts establishes
  the limit on how many cores can be harnessed 

  $$ S(N) = \frac{1}{(1 - s) / N + s}, \quad \textrm{s=non-parallel fraction} $$


# Parallel programming {.section}


# Programming languages

- The de-facto standard programming languages in HPC are (still!)
  C/C++ and Fortran 
- Higher level languages like Python and Julia are gaining popularity
    - Often computationally intensive parts are still written in C/C++
      or Fortran 
- For some applications there are high-level frameworks with
  interfaces to multiple languages
    - Petsc, Trilinos, Kokkos 
    - TensorFlow for deep learning
    - Spark for MapReduce


# Parallel programming models

- Parallel execution is based on threads or processes (or both) which
  run at the same time on different CPU cores 
- Processes
    - Interaction is based on exchanging messages between processes
    - MPI (Message passing interface)
- Threads
    - Interaction is based on shared memory, i.e. each thread can
      access directly other threads data 
    - OpenMP, pthreads


# Parallel programming models

 ![](img/processes-threads.svg){.center width=80%}
<div class=column>
**MPI: Processes** 

- Independent execution units 
- MPI launches N processes at application startup
- Works over multiple nodes
</div>
<div class=column>

**OpenMP: Threads**  

- Threads share memory space
- Threads are created and destroyed  (parallel regions)
- Limited to a single node

</div>

<!--
# Group work: how to parallelize a problem? {.section}

# Smoothed Particle Hydrodynamic simulation

<div class=column>
- Particles are hard spheres
- They interact with neighbours inside some effective range
    - This way the particles appear “viscous”
- See live demo 

</div>
<div class=column>

 ![](img/smooth_particle.svg){.center width=80%}

</div>

# Group work assignment

- Within your group, discuss the Smoothed Particle Hydrodynamic (SPH) problem
- Make back-of-an-envelope sketches, etc, on how would you parallelize
  the selected problem 
    - Which parts of the work can be carried out independently and in parallel?
    - What kind of coordination between the parallel tasks is needed?
- If you come up with several approaches, discuss their pros and cons

-->
