<!-- Adapted partly from material by EPCC -->
<!-- https://github.com/EPCCed/archer2-MPI-2020-05-14 -->


# Summary of day 2

Questions can be copy-pasted e.g. to HackMD.


1. How can boundary MPI tasks *e.g.* in communication chain be treated?

   A. Using `MPI_PROC_NULL`
   B. Using `MPI_ANY_SOURCE`
   C. By putting MPI calls into "if-else" structures
   D. With the help of "modulo" operations

   A.
   B.
   C.
   D.

Correct: A, C, D

C is generally not recommended as code becomes often
cluttered. MPI_PROC_NULL is useful with finite chains where boundary
domains only send or receive. In periodic chains, modulo operations can
be used for wrapping destination and source ranks. (MPI_Cart_create is
even more convenient in most cases but not discussed in this course).

2. Which of the following statements apply to `MPI_Sendrecv`?

   A. It is required for correct functioning of MPI programs
   B. It is syntactic sugar in MPI
   C. It is useful preventing deadlocks
   D. It is useful for avoiding serialization of communication

   A.
   B.
   C.
   D.

Correct: C, D

Correctly functioning programs can be written just with MPI_Send and
MPI_Recv, however, combined Sendrecv can prevent deadlocks and improve
performance. 

3. Which of the following statements about Collective communication
   are correct?

   A. All the processes participate in pairwise communication
   B. Every process sends a message to a specific process
   C. All the MPI tasks within a communicator communicate along
      the chosen pattern
   D. Every process receives messages from every other process
  
   A.
   B.
   C.
   D.

Correct: C

B. and D. may happen with some collective call over MPI_COMM_WORLD,
but are not true for collective communication in general

4. The benefits of collective communication are

   A. There is no benefit
   B. Code is more compact
   C. MPI library can utilize special hardware
   D. MPI library can utilize efficient implementations
  
   A.
   B.
   C.
   D.

Correct: B, C, D

More compact code means typically also easier maintenance. Many
collective patterns can be implemented efficiently e.g. with
tree-based algorithms which can be quite complicated, so even though
user could implement the algorithm with p2p operations, the
implementation in MPI library is most likely more
optimal. Furthermore, supercomputers can have special hardware for
collective operations, whose use requires lower level programming than
MPI. 

5. What is the outcome of the following code snippet when run with 4 processes?
   ```fortran
   a(:) = my_id
   call mpi_gather(a, 2, MPI_INTEGER, aloc, 2, MPI_INTEGER, 3, MPI_COMM_WORLD, rc)
   if (my_id==3) print *, aloc(:)
   ```

   A. "0 1 2 3"
   B. "2 2 2 2 2 2 2 2"
   C. "0 0 1 1 2 2 3 3"
   D. "0 1 2 3 0 1 2 3"
  
   A.
   B.
   C.
   D.

Correct: C
  
6. What is the outcome of the following code snippet when run with 8
   processes, i.e. on ranks 0, 1, 2, 3, 4, 5, 6, 7
   ```c
   if (rank % 2 == 0) { // Even processes
      MPI_Allreduce(&rank, &evensum, 1, MPI_INT, MPI_SUM, MPI_COMM_WORLD);
      if (0 == rank) printf("evensum = %d\n", evensum);
   } else { // odd processes
      MPI_Allreduce(&rank, &oddsum, 1, MPI_INT, MPI_SUM, MPI_COMM_WORLD);
      if (1 == rank) printf("oddsum = %d\n", oddsum);
   }
   ```
   
   A. evensum = 16, oddsum = 12
   B. evensum = 28, oddsum = 28
   C. evensum = 12, oddsum = 16
   D. evensum = 6, oddsum = 2

   A.
   B.
   C.
   D.

Correct: B

This is the closest we have to a trick question, written to make it
look like the correct answer is C!

Although the reduction operation is called from different lines by
different processes, they are an participating in **the same**
reduction as the communicator is MPI_COMM_WORLD. 
As a result, every process computes the sum of all the ranks which is
28, the only difference is that even ranks store the result in
evensum, odd ranks in oddsum. 

Although you often have all processes executing a reduction from the
same line of code, there is no requirement to do this. In fact, as a
library, MPI has no way of knowing where the reduction call was made,
all it knows is that, as it is across MPI_COMM_WORLD, it needs to wait
until all processes have called the routine. If you wanted to compute even
and odd rank sums separately, you would need to split MPI_COMM_WORLD
into two sub-communicators, one for even ranks and the other for odd,
and call then reduction on this subcommunicaior.
	
  
7. Which of the following statements apply to non-blocking communication?
	
   A. Communication happens in the background during computation
   B. Latency is smaller and bandwidth better than with blocking
   routines
   C. There is possibility for overlapping communication and computation
   D. Non-blocking routines can have a small performance penalty
  
   A.
   B.
   C.
   D.

Correct: C, D

Whether communication happens in the background during computation
depends on the MPI implementation and on the underlying hardware, but
non-blocking routines provide **possibility** for that. Communication
performance is not *per se* better than with blocking routines, and in
principle the treatment of requests incurs small overhead, although in
many cases negligible. However, by initiating several communications
(i.e. all directions in 3d halo exchange) one can in some cases
achieve higher aggregate bandwidth.

7. What is outcome of the following code snippet?
   ```cpp
   if (0 == myid) {
      int a = 4;
	  MPI_Isend(&a, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &req);
	  a = 6;
	  MPI_Wait(&req, MPI_STATUS_IGNORE);
   } else if (1 == myid) {
      int a;
	  MPI_Irecv(&a, 1, MPI_INT, 1, 0, MPI_COMM_WORLD, &req);
	  std::cout << "a is: " << a << std::endl;
      MPI_Wait(&req, MPI_STATUS_IGNORE);
  }
  ```
  
   A. 4
   B. 6
   C. Segmentation fault
   D. Not well defined

   A.
   B.
   C.
   D.


Correct: D

  The input buffer of `Isend` should not be modified before the
  communication is finalized with `Wait` or `Test` etc. Also, the
  output buffer of `Irecv` should not be read before receive is
  finalized. In practice, if the data was not received yet before
  printout, the outcome is 0 (or some random value if 
  compiler did not initialize `a` to 0).  If the communication happened
  to take place before rank 0 modified `a`, outcome could be 4, or 6
  if rank 0 managed to modify `a` before communication was realized.
