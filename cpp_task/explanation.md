\# C++ Task Analysis



\## What the code does



The provided program launches two worker threads that repeatedly execute callback functions.  

Both threads share a common atomic boolean flag ('running') used for cooperative shutdown.



Each thread stops when one of the following conditions occurs:



1\. The callback function returns 'true' (abort signal)

2\. The callback execution time exceeds the defined timeout



When one thread stops, the shared 'running' flag is set to 'false' , which causes all threads to terminate.



\## Typical Output



C1: 3 C2: 5





\## Issues Found



1\. loop counters are not thread-safe.

2\. Generic lambda captures all by reference.

3\. high\_resolution\_clock not ideal for elapsed timing.

4\. No exception handling in thread.

5\. Timeout measures total thread lifetime instead of per iteration.







\## Fixes Applied



1\. Atomic counters: Replaced counters with 'std::atomic<int>'

2\. Used explicit lambda capture list

3\. Used steady\_clock

4\. Added exception safety

5\. Better timeout semantics





