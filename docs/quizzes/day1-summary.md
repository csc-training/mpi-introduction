<!-- Adapted from material by EPCC -->
<!-- https://github.com/EPCCed/archer2-MPI-2020-05-14 -->

1. What is MPI?

(A.) the Messagee Passing intertace
B. the Miami Police Investigators
C. the Minimal Polynomial instantiation
D. the Millipede Podiatry institution
(E.) a way of doing distributed memory parallel programming

2. To compile and run an MPl program requires

A. special compilers
(B.) special libraries
C.  a special parallel computer
D. a special operating system
(E.) a launcher program and runtime system


 MPI is a library and therefore requires no special compiler support:
 MPI “compilers“ such as mpicc/mpif90 are just convenient wrappers
 round standard compilers e.g. GNU, Intel or Cray that automatically
 set up appropriate include and link paths. MPI works by having
 multiple processes. Any modern OS can run multiple processes, even
 on a single core CPU.

3. After initiating an MPI program with "mpiexec -n 4
./my_mpi-_rogram", what does the call to MPI_Init() do?

A. create the 4 parallel processes
B. start program execution
(C.) enable the 4 independent programs subsequently to communicate with each other
D. create the 4 parallel threads

The standard MPl launchers (mpiexec, mpirun, srun, aprun etc.) create
multiple copies of your MPI executable, each being a separate OS
process; there are already multiple processes running before the call
to MPI_Init. These will happily run independently - what the MPI_Init 
call does is get them to talk to each other, which is required before
any user-initiated communications.

4. If you call MPI_Recv and there is no incoming message, what happens?

A. the Recv fails with an error
B.  the Recv reports that there is no incoming message
(C). the Recv waits until a message arrives (potentially waiting forever)
D. the Recv times out after some system specified delay (e.g. a few
minutes)

MPI is not fault tolerant and assumes all processes are alive all the
time. There are therefore no timeouts in MPI. The assumption is that
you have written a correct program and that a matching message will
(eventually) arrive. This means that an unmatched receive waits
forever.

5. If you call MPI_Send and there is no matching receive, which of the
   following are possible outcomes? 
   
A. the message disappears 
B. the send fails with an error
(C.) the send waits until a receive is posted (potentially waiting forever)
(D.) the message is stored and delivered later on (if possible)
E. the send times out after some system pecified delay (e.g. a few minutes)
(F.) the program continues execution regardless of whether the message
is received 

MPI_Send is blocking, so its completion *may* depend on the matching
receive. In practice, MPI will try to buffer small messages but,
rather than failing if there is insufficient buffer space, will switch
to using synchronous send for large messages. However, the threshold
to switch between the two is implementation dependent (and might even
change depending on how many processes you are running on) so you must
always handle the case that MPI_Send might be synchronous (or use
other MPI calls which will be discussed later on).

6. The MPI receive routine has a parameter "count". What does this mean? .
A. the size of the incoming message (in bytes)
B. the size of the incoming message (in items, e.g. integers)
C. the size of the buffer you have reserved tor storing the message in bytes
(D.) the size of the buffer you have reserved for storing the message in items (e.g integers)

MPI tries to avoid talking about bytes - counting is almost always
done in number or items. For the receive, count is the size of the
local receive buffer, not of the incoming send buffer, although of
course in some programs they may be the same. 

7. What happens if the incoming message is larger than “count"

(A.) the receive dails with an error
B. the receive reports zero data received
C. the message writes beyond the end or the available storage
D. only the first "count" items are received 

MPI checks that the incoming message will fit into the supplied
storage before receiving it. The standard behaviour on error is for
the whole MPI program to exit immediately with a fatal error

8. What happens if the incoming message (of size "n") is smaller than "count"

A. the receive fails with an error
B. the receive reports zero data received
(C.) the first "n" items are received
D. the first "n" items are received and the rest of the storage is zeroed

This is entirely legal. In simple programs (e.g regular-grid
halo-swapping) you may know the size of all messages so #n" and
"count" might always be the same. However, in other situations
(e.g. particle-based codes) you may not know how many items are being
sent so you must ensure that you have enough storage locally

9. How is the actual size of the incoming message reported?

A. the value of "count" in the receive is updated.
B. MPI cannot tell you
C. it is stored in the Status parameter
D. via the associated tag

Various pieces of metadata about the received message are stored in
the Status such as the origin and tag (useful if you are using
wildcarding) and its size. Unfortunately, the size is not available
directly, you must pass the status to the helper routine MPI_Get_count.

10. Which of the following are possible outputs from this piece of
    code run on 3 processes: 

```
printf("Welcome from rank %d\n“, rank);
printf("Goodbye lrom rank %d\n", rank);
```

(A.)  Welcome from rank 0
      Welcome from rank 1
      Welcome from rank 2
      Goodbye from rank 0
      Goodbye from rank 1
      Goodbye from rank 2
	 
B.  Welcome from rank 2
	Welcome from rank 1	
	Goodbye from rank 0
	 Goodbye from rank 1
	 Goodbye from rank 2
	 Welcome from rank 0

(C.)  Welcome from rank 2
    Goodbye from rank 2
    Welcome from rank 0
    Welcome from rank 1
    Goodbye from rank 1
    Goodbye from rank 0
	
D.  Welcome from rank 0
    Goodbye from rank 1
    Welcome from rank 2
    Goodbye from rank 0
	Welcome from rank 1
    Goodbye from rank 2
	
The output from dillerenl MPI processes is interleaved in an
unpredictable way. The order in which two print statements appear on
the screen has, in general, nothing to do with when the priim
statements actually happened. Remember that the output from many 
separate programs running on many different computers is appearing on a
single screen - a lot of bufering etc. may take place between a print
statement executing and the output appearing on the screen in
particular, process x may execute a print statement before 
process Y, but the output could appear in the other order (i.e. Y
appears before X ).

However, the ordering from an indivrdual process will be maintained,
so for a particular process "X" the "Goodbye" from X will always
appear after the "Welcome" from X. 

11. Which of the following statements do you agree with regarding this code:

```
for (i=0; i < size; i++)
 { 
    if (rank == i)
      {
        printf("Hello frorm rank %d\n", rank);
        j = 10*i;
      }
 }
```

A. The for loop ensures the operations are in order: rank 0, then rank
1, ...
B. The for loop ensures the operation are done in parallel across all processes
(C.) The for loop is entirely redundant
D. The final value of j will be equal to 10*(size-1)

 Remember that every MPI process is executing the entire piece of code
 so each process only ever executes the if statement once when `rank ==
 i` The for loop is therefore
redundant, the above code produces exactly the same result as:

```
  printf("Hello frorm rank %d\n", rank);
  j = 10*i;
```
