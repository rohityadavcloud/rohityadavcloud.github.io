---
layout: page
title: Systems
tagline: List kernel, compiler etc.
---

## Hack

- x86 kernel [JS linux](https://github.com/levskaya/jslinux-deobfuscated)
- Toy Distributed file system like ceph/gluster
- Toy Compiler
- Toy Database

### Distributed Systems

- [Distributed system lectures/talks](http://research.microsoft.com/apps/catalog/default.aspx?p=1&sb=no&ps=25&t=videos&sf=&s=&r=&vr=166581&ra=)
- [Distributed Systems principles course](http://dcg.ethz.ch/lectures/podc_allstars/)
- [Distribute systems course in Go](http://www.cs.cmu.edu/~dga/15-440/F12/syllabus.html)
- Raft
- Consistent hashing
- Google tech: Borg, Bigtable, Spanner, Mapreduce, Millwheel, LMCTFY
- [Quorum read/writes](https://en.wikipedia.org/wiki/Quorum_%28distributed_computing%29)
- [Vector Clocks](https://en.wikipedia.org/wiki/Vector_clock)

### Compilers

- http://mattias-.github.io/2015-01-03/DIY-Make-Your-Own-Programming-language/
- http://stackoverflow.com/questions/1669/learning-to-write-a-compiler
- http://homepage.ntlworld.com/edmund.grimley-evans/cc500/
- http://compilers.iecc.com/crenshaw/
- https://github.com/rui314/8cc (http://www.sigbus.info/how-i-wrote-a-self-hosting-c-compiler-in-40-days.html)
- http://bellard.org/tcc/
- http://llvm.org/docs/tutorial/
- Compilers [Stanford](https://class.coursera.org/compilers-003/lecture)
- [Automata](https://class.coursera.org/automata-002/lecture) by Ullman
- [Toy compiler](https://github.com/lsegal/my_toy_compiler), [Writing own compiler](http://gnuu.org/2009/09/18/writing-your-own-toy-compiler/)
- [Build your own Lisp](http://www.buildyourownlisp.com/contents)
- http://research.microsoft.com/en-us/um/people/simonpj/papers/pj-lester-book/
- Interpretor: http://ruslanspivak.com/lsbasi-part1/

#### Compiler articles

- http://blog.jamesdbloom.com/JVMInternals.html
- http://blog.altoros.com/golang-internals-part-2-diving-into-the-go-compiler.html

### Networking

- http://beej.us/guide/bgnet/output/html/singlepage/bgnet.html
- http://intronetworks.cs.luc.edu/

#### Infrastructure workouts

- Design and implement a durable complex event-processing service with exactly-once semantics.
- Create an internal framework for writing new API endpoints
- You've scaled a large system before. You realize that 95% of scaling is doing nitty-gritty detail work.
- You have extensive experience with server operations, though you lean more towards writing code.
- Design and implement a distributed authentication service
- Implement efficient realtime queries on top of MongoDB by consuming the MongoDB replication log.
- Add features to DDP, Meteor's state synchronization protocol. For example, you might add protocol-level support for pagination of realtime datasets, and write a DDP caching proxy that performs this pagination in a separate tier from the application logic.
- Write a kernel for a distributed operating system that schedules application processes across a set of machines. Make it capable of updating itself in place without any downtime.
- Implement an advanced package dependency resolver on top of a pseudoboolean SAT solver.
- Perform static analysis on JavaScript to automatically determine the correct load order for the files, when the app developer hasn't specified it.
- Write a full implementation of SQL that runs in-memory in a web browser, and also supports arbitrary realtime queries and speculative local execution of updates that are in flight to the server.

