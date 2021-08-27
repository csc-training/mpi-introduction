---
title:  Performance analysis
author: CSC Training
date:   2021
lang:   en
---

# Performance analysis {.section}

# Performance analysis

![](img/perf-analysis-mpi.svg){.center width=60%}

# Introduction

- Finding out the scalability bottlenecks of parallel application is non-trivial
- Bottlenecks can be very different with few CPUs than with thousands of CPUs
- Performance analysis needs to be carried in scalability limit with 
  large enough test case
- Efficient tools are needed for the analysis of massively parallel applications

# Potential scalability bottlenecks

- Bad computation to communication ratio
    - In 2D heat equation with one dimensional parallelization 
      $\frac{T_{comp}}{T_{comm}} \sim \frac{N}{p}$

- Load imbalance
- Synchronization overhead
- Non-optimal communication patterns

# Common inefficient communication patterns 

![](img/ineff1.svg){.center width=80%}

# Measuring performance

- Timing routines within the application
    - MPI includes high resolution timer `MPI_Wtime`
    - Can be useful for big picture

```c++
double t0 = MPI_Wtime()
do_something()
double t1 = MPI_Wtime()
printf("Elapsed time %f\n", t1 - t0);
```
- Dedicated parallel performance analysis tools
- ScoreP / Scalasca, Extrae / Paraver, Intel Trace Analyzer, TAU, CrayPAT, Vampir


# MPI performance analysis tools

- Many tools can automatically identify common problematic communication 
   patterns
- Flat profile
    - Time spent in MPI calls during the whole program execution
- Trace
    - Also the temporal profile of MPI calls
    - Potentially huge data
	    - Filtering only the most relevant calls may be needed

# Demo: Trace analysis {.section}

# Summary

- Many tools can provide information about MPI performance problems
    - Manual investigation impossible in massively parallel scale
- Problems often caused by load imbalance or by badly designed communication 
  pattern 


# Common communication patterns {.section}

# Main - worker 

![](img/comm_patt1.svg){.center width=100%}

- Each process is only sending or receiving at the time

# Pairwise neighbour communication

![](img/comm_patt2.svg){.center width=100%}

<br>

- Incorrect ordering of sends/receives may give a rise to a deadlock
  (or unnecessary idle time)
- Can be generalized to multiple dimensions

# Combined send & receive 

MPI_Sendrecv(`sendbuf`{.input}, `sendcount`{.input}, `sendtype`{.input}, `dest`{.input}, `sendtag`{.input}, `recvbuf`{.input}, `recvcount`{.input}, `recvtype`{.input}, `source`{.input}, `recvtag`{.input}, `comm`{.input}, `status`{.output})
  : `-`{.ghost}
    : `-`{.ghost}

- Sends one message and receives another one, with a single command
    - Reduces risk for deadlocks and improves performance
- Parameters as in `MPI_Send` and `MPI_Recv`
- Destination rank and source rank can be same or different
- `MPI_PROC_NULL` can be used for coping with the boundaries

# Summary

- Individual `MPI_Send` and `MPI_Recv` are suitable for irregular communication
- When there is always both sending and receiving, `MPI_Sendrecv` can prevent deadlocks
  and serialization of communication


