# Questions

The questions section. Need to import this to the slides when done.

---

# Questions that can arise

## From the paper's own words

Section 2.2, Initialization and Read-Sharing, p8
> Then Eraser will detect the error if any of the second thread’s accesses occur before the first thread’s final initialization actions, but otherwise Eraser will miss the error. We do not think this has been a problem, but *we have no way of knowing for sure.*

Scheduler interleaving might cause a data race to be missed, or worse make this a non-deterministic finding. Not really comfortable with the "we have no way of knowing for sure" part.

Section 3.2, Performance, p10
> Applications typically slow down by a factor of 10 to 30 while using Eraser. *This time dilation can change the order in which threads are scheduled and can affect the behavior of time-sensitive applications.* Our experience suggests that differences in thread scheduling have little effect on Eraser’s results. We have less experience with very time-sensitive applications, and it is possible that they would benefit from a more efficient monitoring technique.

They admit that the slowdown can cause thread interleavings to change and thus cause some data races to be missed but then say that the interleaving would have little effect on Eraser's results. How believeable is this statement?

Section 3.4, Race Detection in an OS Kernel, p12
> Another difference is that operating systems make greater use of post/ wait style synchronization...
> ...Because semaphores are not “owned” it is difficult for Eraser to infer which data they are being used to protect, leading it to issue false alarms.

A question one might have is whether synchronization primitives other than locks can be used for this lockset algorithm. The answer is no.

Section 4.1, AltaVista, p14
> We found a large number of reported races, most of which turned out to be false alarms. These were primarily caused by memory reuse, followed by private locks and benign races. The benign races found in Ni2 are particularly interesting, because they exemplify the intentional use of races to reduce locking overhead.

Another instance:

> We also found a benign race in the Ni2 test harness program, where multiple threads race on reads and writes to a global variable called kill queries. This variable is initialized to false and is set to true to indicate that all threads should exit...
> ...To keep the race detector from reporting such races, we used the EraserIgnoreOn/Off( ) annotations...
> ...Inserting nine annotations in the Ni2 library, five in the ft test harness, and 10 in the mhttpd server reduced the number of reported races from more than a hundred to zero.

Who certifies that a race is benign in nature? They design EraserIgnoreOn/Off for these benign conditions (and memory resuse, private locks) but it leaves a lot to the user to configure. 
Another bone I have with this is that some modification of the original source code is required for Eraser to work -- the EraserIgnoreOn/Off module mentioned now.
A clean repot requires editing the source which can hide future bugs.

## Generic questions I can think of

- Is data race freedom the only goal? What about deadlocks or atomocity violations?
- Mentioned earlier but what if it's not a lock? Semaphores? Atomics like atomic_add or __atomic_fetch_add? compare and swap? 
- This ties into the previous question -- the Eraser lockset algorithm was a general idea but the implementation is specific to alpha and the alpha programming model/architecture. If this was being designed for modern day linux, how would it look like?
- Reordering of threads is a big issue that they *gloss over*. If so, how could it be or is that solved in modern variations of dynamic data race detectors?

## Modern outlook on dynamic data race detectors

- How do modern dynamic data race detectors look like? Do they still use lockset? If not what do they use?
- Keeping Eraser on all the time gave a 10-30X slowdown. What do modern detectors do to tackle this?
- Static and dynamic data race checkers. How do the two combine? Are they still separate?
