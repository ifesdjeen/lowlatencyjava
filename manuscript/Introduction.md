Most problem are quite straightforward to solve: when something is slow,
you can either optimize it or parallelize it. When you hit a throughput
barrier, you partition things to more workers. Although when you face
problems that involve Garbage Collection pauses or simply hit the limit
of the virtual machine you're working with, it gets much harder to fix
them.

When you're working on top of a Virtual Machine, you may face things 
that are simply out of your control. Namely, time drifts and latency.
Gladly, there are enough battle-tested solutions, that require a bit
of understanding of JVM internals and the way things work.

Over the course of next several months, I'll be releasing articles on
Java Performance Optimisation mechanisms, which will include (but won't
be limited by) subjects: 

  * Compare and Swap operations 
  * Lock operation internals 
  * Working with Off-Heap data
  * 0-zopy on JVM
  * False Sharing / CPU Cache Alignment 
  * Time Drifts and Waiting Strategies 
  * Useful Bit Operations and their application
  * Object Freezing 
  
If you can serve 10K requests per second, conforming with certain
performance (memory and CPU parameters), it doesn't mean that you'll 
be able to liearly scale it up to 20K. If you're allocating too many
objects on heap, or waste CPU cycles on something unnecessary, you'll
eventually hit the wall. 

The simplest (yet very underrated) way of saving up on memory allocations
is object pooling. Even though the concept is sounds similar to just 
pooling objects and socket descriptors, there's a slight difference.
When we're talking about socket descriptors, we have limited, rather 
small (tens, hundreds, or max thousands) of descriptors to go through. 
These resources are pooled because of the high initialization cost
(establishing connection, performing a handshake over the network, 
memory-mapping the file or whatever else). 

