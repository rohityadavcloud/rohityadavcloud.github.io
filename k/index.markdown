---
redirect_from: "/kaizen/"
layout: page
title: Kaizen
---

### Tip

- [USACO training](http://train.usaco.org/usacogate)
- [SPOJ top 100](http://www.spoj.com/problems/classical/sort=-6)
- Algorithms Princeton [level-1](https://class.coursera.org/algs4partI-003/lecture)
- Algorithms Princeton [level-2](https://class.coursera.org/algs4partII-002/lecture)
- [Stanford Algorithms 1](https://www.coursera.org/course/algo)
- [Stanford Algorithms 2](https://www.coursera.org/course/algo2)

### Programming

- [Geeks for geeks](http://www.geeksforgeeks.org/)
- Programming Practice: [InterviewBit](http://interviewbit.com/),
- Programming Practice: [Euler](http://projecteuler.net/),
- Programming Practice: [HackerRank](https://www.hackerrank.com/)
- Programming Practice: [Codeforces](http://codeforces.com/)
- Programming Practice: [UVa](http://uva.onlinejudge.org), [UVa Tool](http://uhunt.felix-halim.net/id/0)
- [Humblefool's CodeSchool](http://mycodeschool.com/problems) [Winter coding @ MyCodeSchool](http://wintercoding.mycodeschool.com/)
- [Algorithms camp](http://www.youtube.com/watch?v=vZ2Wn6Ly8Ok&playnext=1&list=PL713C10F05D6BB7BF)

### Algorithms

- [Algorithm Coding](./algorithms.html) ([refernce impls](https://github.com/kennyledet/Algorithm-Implementations))
- [List of things](http://discuss.codechef.com/questions/48877/data-structures-and-algorithms)
- [Topcoder Algo tutorials](http://community.topcoder.com/tc?module=Static&d1=tutorials&d2=alg_index)
- [Book: The Algo book by Robert Sedgewick](http://algs4.cs.princeton.edu/home/)
- [Book: Analysis of Algorithms](https://www.coursera.org/course/aofa), [An Introduction to the Analysis of Algorithms](http://aofa.cs.princeton.edu/home/)
- [Introduction to Programming in Java](http://introcs.cs.princeton.edu/java/home/)

### Stack

#### Language

- Way forward: Java, Go
- Nicely done: Python, Ruby
- Funky: Scala, Clojure, Elixir
- Powerful evil: C, C++, Rust, Assembly, LLVM
- Gray beard: Haskell, Lisp, Scheme, Erlang, OCaml

#### Web Development

- Frontend langs: HTML5, CSS3, JavaScipt (ES5/6), TypeScript, CoffeeScript, Less/Saas
- Browser tech: Websockets, local storage
- Web dev tools: Grunt, Gulp, Bower, NPM
- JS Frameworks: Backbone, Angular, React, Meteor
- UI frontend framework: Bootstrap, Material Design (Google)

- Web backend frameworks:
  + Ruby (Rails)
  + Java (Dropwizard {jackson, guava, guice, jersey, hibernate, gradle, jetty/netty/slf4j}, Vaadin, Grails, Play)
  + Go
  + Python (Django)

- Framework parts:
  + URL router and handlers
  + Caching
  + Model:
    * DB backends
    * ORM
    * Schema migration
  + APIs
  + static file/binary serving
  + middleware
  + websockets
  + Data validation
  + serialization (html, xml, json)
  + RPC
  + Templates
  + Forms
  + File uploading, storage
  + File generation: csv, pdf
  + Auth:
    * Cookies
    * Session
    * RBAC, Groups, roles, users, teams/orgs
  + Administration
  + i18n, l10n
  + debugging
  + logging
  + monitoring
  + jobs
  + queue
  + search
  + configuration
  + email/alerting
  + unit/integration testing
  + Security:
    * clickjacking
    * XSS
    * CSRF
    * Remote code execution
    * SQL injection
    * Cryptographic signing (sessionkey)
    * cors
    * acceptlang, acceptflags (headers)

#### APIs

- http://swagger.io/
- https://apiary.io/
- https://apiblueprint.org/#get-started

#### Persistence and Processing

- DB: MySQL, PostgreSQL, SQLite, ElasticSearch, Redis, Memcache
- DB ORMs/frameworks: SQLAlchemy, Liquibase, Jooq, Flyway
- Queue: Kafka, RabbitMQ
- Task queue: Celery, Gearman
- RPC: Protocol Buffers, Thrift, Cap n Proto

#### Devops and Infra

- VCS: Git/Github, Mercurial/Bitbucket
- Automation: Ansible, Chef, Bash
- Build system: Maven, Gradle, CMake, Makefiles
- CI: Jenkins, Go CD, BuildBot, Strider
- Logging: Sentry, Graylog, Scribe, Logstash
- Monitoring: Graphite, Promethius, Sensu, Munin, Nagios
- Tracing: Zipkin/Dapper,
- Platform: Libvirt/qemu, Linux
- Locking/consensus: Etcd, Zookeeper
- Experimental: Docker, Weave, Kubernetes, Mesos, Vagrant, Packer, Serf/Consul, Terraform, Vault, Capgemini/Apollo

### Hack

- Accounting/invoicing app (http://webzash.org/image/tid/3, weave?)
- CloudStack QA crowdsourcing app
- CloudStack UI (mostly frontend intensive)
- MergeShack: Code sharing RBAC shack
- Offline buffer-app like mobile app

#### Misc

- Tantra - DIY x86 kernel [JS linux](https://github.com/levskaya/jslinux-deobfuscated)
- LLVM based toy programming language [LLVM Toy using Haskell](http://www.stephendiehl.com/llvm/)
- Toy Distributed file system like ceph/gluster
- Toy Compiler
- Toy Database
- readline in [go](https://github.com/bhaisaab/currycli), example [linenoise](https://github.com/antirez/linenoise), [already](https://github.com/peterh/liner) [existing?](https://github.com/gobs/cmd)
- CLI:
  https://github.com/codegangsta/cli
  https://github.com/spf13/cobra
  https://github.com/spf13/viper
  https://github.com/go-martini/martini
  https://github.com/Unknwon/macaron/
  https://github.com/dropbox/godropbox
  https://github.com/elazarl/goproxy

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

### Stash

- https://github.com/prakhar1989/awesome-courses

- http://www.postgresguide.com
- http://www.oreilly.com/programming/free/files/software-architecture-patterns.pdf
- http://www.cs.virginia.edu/~evans/cs216/guides/x86.html
- http://blog.jamesdbloom.com/JVMInternals.html
- http://blog.altoros.com/golang-internals-part-2-diving-into-the-go-compiler.html
- http://beej.us/guide/bgnet/output/html/singlepage/bgnet.html
- https://www.airpair.com/aws/posts/building-a-scalable-web-app-on-amazon-web-services-p1?wed
- http://intronetworks.cs.luc.edu/
- http://xlinux.nist.gov/dads/
- http://en.wikipedia.org/wiki/List_of_algorithms
- http://danluu.com/new-cpu-features/
- [Git clone in Haskell](http://stefan.saasen.me/articles/git-clone-in-haskell-from-the-bottom-up)
- [What I wish I knew when learning Haskell 2.0](http://dev.stephendiehl.com/hask/#cabal)
- [Large Scale JS](http://addyosmani.com/largescalejavascript/)

### Distributed Systems

- [Distributed system lectures/talks](http://research.microsoft.com/apps/catalog/default.aspx?p=1&sb=no&ps=25&t=videos&sf=&s=&r=&vr=166581&ra=)
- [Distributed Systems principles course](http://dcg.ethz.ch/lectures/podc_allstars/)
- [Distribute systems course in Go](http://www.cs.cmu.edu/~dga/15-440/F12/syllabus.html)

### Kernel

- http://pages.cs.wisc.edu/~remzi/OSTEP/
- http://www.cs.cmu.edu/~410/lecture.html
- http://warsus.github.io/lions-/
- https://www.devimperium.com/tutorials/start_your_small_operating_system_in_assembly
- https://github.com/rust-lang/rust/wiki/Operating-system-development
- http://littleosbook.github.io/
- https://sortix.org/
- OS lectures by Mckusic
- [Bottom Up Learning](http://www.bottomupcs.com/index.html)
- [xv6 and 6.828](http://pdos.csail.mit.edu/6.828/2014/schedule.html)
- [Potato teaching OS](https://github.com/dbader/potatoes)
- [Adelaideos Teaching OS](http://adelaideos.sourceforge.net)
- [OSDev books](http://wiki.osdev.org/Books)
- [GDB](http://beej.us/guide/bggdb/)
- [OSSTEP](http://pages.cs.wisc.edu/~remzi/OSTEP/)
- [Stackoverflow resource](http://stackoverflow.com/questions/43180/what-are-some-resources-for-getting-started-in-operating-system-development)
- [How to Make a Computer Operating System](http://samypesse.github.io/How-to-Make-a-Computer-Operating-System/)
- [OS Study Guide](http://www.sal.ksu.edu/faculty/tim/ossg/index.html)
- [How to Kernel basics](http://www.osdever.net/bkerndev/Docs/intro.htm)
- [JoelG's bootloader stuff](http://joelgompert.com/OS/TableOfContents.htm)
- [Inside Linux boot process](http://www.ibm.com/developerworks/library/l-linuxboot/index.html)
- [Kernel boot process](http://duartes.org/gustavo/blog/post/kernel-boot-process/)
- [How computer boots up](http://duartes.org/gustavo/blog/post/how-computers-boot-up/)
- [Intro to 8086](http://www.csi.ucd.ie/staff/jcarthy/home/alp/alp-05.pdf)
- [Writing simple OS from Scratch](http://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf)
- [OS and related links](http://www.superfrink.net/athenaeum)

### Maths

- http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-042j-mathematics-for-computer-science-fall-2010/readings/MIT6_042JF10_notes.pdf
- [Maths for computer science](http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-042j-mathematics-for-computer-science-fall-2010/video-lectures/)

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

### Read

- Log Structured Merge tree
- Merkel trees
- Raft
- Consistent hashing
- Google tech: Borg, Bigtable, Spanner, Mapreduce, Millwheel, LMCTFY
- [Quorum read/writes](https://en.wikipedia.org/wiki/Quorum_%28distributed_computing%29)
- [Vector Clocks](https://en.wikipedia.org/wiki/Vector_clock)
- Memory allocation and garbage collection
- [Eudyptula Challenge: Linux programming](http://eudyptula-challenge.org/) | [Linux Kernel Module Programming](http://www.tldp.org/LDP/lkmpg/2.4/html/book1.htm)

### Books

- [Linux Internals](http://0xax.gitbooks.io/linux-insides/content/)
- [JS desing patterns](http://addyosmani.com/resources/essentialjsdesignpatterns/book/#mediatorpatternjavascript)
- [Game programming patterns](http://gameprogrammingpatterns.com/index.html)

#### Deep learning

- [Deep learning list](http://jmozah.github.io/links/)
- [Machine Learning](https://www.coursera.org/course/ml)
- [Neural Network](https://www.coursera.org/course/neuralnets)
- [Deep Learning Python tutorial](http://deeplearning.net/tutorial/deeplearning.pdf)
- [Deep Learning MIT book](http://www.iro.umontreal.ca/~bengioy/dlbook/)

### Closet

- [Lambda Calculus](https://www.youtube.com/playlist?list=PL4A05CF0478DAD704)
- Checkout [projects](https://github.com/karan/Projects)
- [Haskell](http://www.scs.stanford.edu/11au-cs240h/)
- [Scala Lectures](https://class.coursera.org/progfun-003/lecture) by Martin Odesky
- [Reactive Programming](https://class.coursera.org/reactive-001/lecture) M. Odesky
- [SICP Lectures](http://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-001-structure-and-interpretation-of-computer-programs-spring-2005/video-lectures/)
- [Interactive SICP](http://xuanji.appspot.com/isicp/index.html)
- [Stanford Cryptography 1](https://www.coursera.org/course/crypto)
- [Stanford Cryptography 2](https://www.coursera.org/course/crypto2)

### 2016

- Backend (Java 8, Go), UI stack, Android
- Programming practice or projects
- Algorithms
- Distributed Systems
- OS + DIY kernel
- Compilers + DIY compiler
