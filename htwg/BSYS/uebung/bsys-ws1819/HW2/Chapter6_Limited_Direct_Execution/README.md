# MEASUREMENT HOMEWORK - Chapter 6: Limited Direct Execution

In this homework, you’ll measure the costs of a system call and context switch.
Measuring the cost of a system call is relatively easy. For example, you could
repeatedly call a simple system call (e.g., performing a 0-byte read), and time
how long it takes; dividing the time by the number of iterations gives you an
estimate of the cost of a system call.

One thing you’ll have to take into account is the precision and accuracy of your
timer. A typical timer that you can use is `gettimeofday()`; read the man page
for details. What you’ll see there is that `gettimeofday()` returns the time in
microseconds since 1970; however, this does not mean that the timer is precise
to the microsecond. Measure back-to-back calls to `gettimeofday()` to learn
something about how precise the timer really is; this will tell you how many
iterations of your null system-call test you’ll have to run in order to get a
good measurement result.  If `gettimeofday()` is not precise enough for you, you
might look into using the `rdtsc`instruction available on x86 machines.

Measuring the cost of a context switch is a little trickier. The lmbench
benchmark does so by running two processes on a single CPU, and setting up two
UNIX pipes between them; a pipe is just one of many ways processes in a UNIX
system can communicate with one another. The first process then issues a write
to the first pipe, and waits for a read on the second; upon seeing the first
process waiting for something to read from the second pipe, the OS puts the
first process in the blocked state, and switches to the other process, which
reads from the first pipe and then writes to the second. When the second process
tries to read from the first pipe again, it blocks, and thus the back-and-forth
cycle of communication continues. By measuring the cost of communicating like
this repeatedly, lmbench can make a good estimate of the cost of a context
switch. You can try to recreate something similar here, using pipes, or perhaps
some other communication mechanism such as UNIX sockets.

One difficulty in measuring context-switch cost arises in systems with more than
one CPU; what you need to do on such a system is ensure that your
context-switching processes are located on the same processor. Fortunately, most
operating systems have calls to bind a process to a particular processor; on
Linux, for example, the `sched_setaffinity()` call is what you’re looking for.
By ensuring both processes are on the same processor, you are making sure to
measure the cost of the OS stopping one process and restoring another on the
same CPU.
