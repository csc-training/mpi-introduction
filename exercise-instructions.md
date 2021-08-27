## General exercise instructions

For most of the exercises, skeleton codes are provided both for
Fortran and C/C++ in the corresponding subdirectory. Some exercise
skeletons have sections marked with “TODO” for completing the
exercises. In addition, all of the 
exercises have exemplary full codes (that can be compiled and run) in the
`solutions` folder. Note that these are seldom the only or even the best way to
solve the problem.

The exercise material can be downloaded with the command

```
git clone https://github.com/csc-training/mpi-introduction.git
```

If you have a GitHub account you can also **Fork** this repository and clone then your fork.

### Computing servers

Exercises can be carried out using the CSC's Puhti supercomputer. See [here](https://docs.csc.fi/support/tutorials/puhti_quick/) 
for general instructions on using Puhti.

In case you have working parallel program development environment in your
laptop (Fortran or C compiler, MPI development library, etc.) you may also use
that. Note, however, that no support for installing MPI environment can be provided during the course.

Puhti can be accessed via ssh using the
provided username (`trainingxxx`) and password:
```
ssh -Y training000@puhti.csc.fi
```


For editing program source files you can use e.g. *nano* editor: 

```
nano prog.f90 &
```
(`^` in nano's shortcuts refer to **Ctrl** key, *i.e.* in order to save file and exit editor press `Ctrl+X`)
Also other popular editors (emacs, vim, gedit) are available.

### Disk areas

All the exercises in the supercomputers should be carried out in the
**scratch** disk area. The name of the scratch directory can be
queried with the command `csc-workspaces`. As the base directory is
shared between members of the project, you should create your own
directory:
```
cd /scratch/project_2000745
mkdir -p $USER
cd $USER
```


## Compilation and running

### MPI

Compilation of the MPI programs can be performed with the `mpif90`,
`mpicxx`, and `mpicc` wrapper commands:
```
mpif90 -o my_mpi_exe test.f90
```
or
```
mpicxx -o my_mpi_exe test.cpp
```
or
```
mpic -o my_mpi_exe test.c
```

The wrapper commands include automatically all the flags needed for building
MPI programs.

#### Running in Puhti

In Puhti, programs need to be executed via the batch job system. Simple job running with 4 MPI tasks can be submitted with the following batch job script:
```
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --account=project_2000745
#SBATCH --partition=small
#SBATCH --reservation=mpi_intro
#SBATCH --time=00:05:00
#SBATCH --ntasks=4

srun my_mpi_exe
```
Replace `<project>` with the project id provided together with your access credentials. Save the script *e.g.* as `job.sh` and submit it with `sbatch job.sh`. 
The output of job will be in file `slurm-xxxxx.out`. You can check the status of your jobs with `squeue -u $USER` and kill possible hanging applications with
`scancel JOBID`.

#### Running in local workstation

In most MPI implementations parallel program can be started with the `mpiexec` launcher:
```
mpiexec -n 4 ./my_mpi_exe
```

