---
layout: post
title: CloudStack Cloudmonkey
excerpt: python powered command line interface
---

About 2-3 weeks ago I started writing a CLI (command line interface) for [Apache CloudStack](http://incubator.apache.org/cloudstack). I researched some options and finally chose Python and cmd. Python comes preinstalled on almost all Linux distros and Mac (Windows I don't care :P it's not developer friendly), cmd is a standard package in Python with which one can write a tool which can work as a command line tool and as an interactive shell interpretor. I named it `cloudmonkey` after the project's mascot. In this blog and elsewhere I use the name as Cloudmonkey or cloudmonkey, but not CloudMonkey :P

<center><img src="/images/apache/cloudmonkey-mac.png"><br><p>Cloudmonkey on OSX</p></center>

Apache CloudStack has around 300 restful [APIs](http://incubator.apache.org/cloudstack/docs/api/index.html) give or take, and writing handlers (autocompletion, help, request handlers etc.) seemed a mammoth task at first. Marvin (the ignored robot) came to rescue. Marvin is a Python package within CloudStack and was written by [Edison](http://www.linkedin.com/pub/disheng-su/5/ab9/90b) and now maintained by [Prasanna](http://in.linkedin.com/in/v0g0n) which provides bunch of classes with which one can implement a client for CloudStack and provides cloudstackAPI. It's interesting how cloudstackAPI is generated. A developer writes an API and fills the boilterplate with API specific details such as required params etc. and java doc string. This information is picked up by an api writer class which generates an xml containing information about each API, its docstring and parameters. This is used by _apidocs_ artifact to generate API help docs and used by Marvin's code generator to create a module for cloudstackAPI which contains command and response classes. When I understood the whole process I thought if I can reuse this somehow I won't have to deal with the 300 APIs directly. <br>

<center><img src="/images/apache/cloudmonkey-ubuntu.png"><br><p>Cloudmonkey on Ubuntu</p></center>

I've always been a fan of functional programming, iterative or object oriented programming was not going to help. So, I grouped the apis based on their first lowercase chars, for example for the api listUsers, the verb is list. Based on such pattern, I wrote the code so that it would group APIs based on such verbs and create handlers on the fly and add them to the shell class. The handlers are actually closures so, this way every handler is actual a dynamic function in memory enclosed by the closure generator for a verb. In the initial version, when a command was executed first time based on its verb, command class from appropriate module from cloudstackAPI would be loaded and a cache dictionary would be populated if a cache miss was hit. In later version, I wrote a cache generator which would precache all the APIs at build time to cheat on the runtime lookup overhead from O(n) to O(1). This cache would contain for each verb the api name, required params, all params and help strings. This dictionary is used for autocompletion for the verbs, the commands and their parameters, and for help strings.

<pre class="prettyprint linenums">
grammar = ['list', 'create', 'update', 'delete', ...]
for rule in grammar:
    def add_grammar(rule):
        def grammar_closure(self, args):
            if not rule in self.cache_verbs:
                self.cache_verb_miss(rule)
            try:
                args_partition = args.partition(" ")
                res = self.cache_verbs[rule][args_partition[0]]

            except KeyError, e:
                self.print_shell("Error: invalid %s api arg" % rule, e)
                return
            if ' --help' in args or ' -h' in args:
                self.print_shell(res[2])
                return
            self.default(res[0] + " " + args_partition[2])
        return grammar_closure
</pre>

Right now `cloudmonkey` is available as a community distribution on the [cheese shop](http://pypi.python.org/pypi/cloudmonkey/), so `pip install cloudmonkey` already! It has a [wiki](https://cwiki.apache.org/confluence/display/CLOUDSTACK/CloudStack+cloudmonkey+CLI) on building, installation and usage instructions, or watch a [screencast](http://www.youtube.com/watch?v=BjkGp3egv9g) ([transcript](http://people.apache.org/~bhaisaab/cloudstack/cloudmonkey/cloudmonkey-screencast-user-transcript.txt), [alternate link](http://people.apache.org/~bhaisaab/cloudstack/cloudmonkey/cloudmonkey-screencast-user.mov)) I made for users. As the userbase grows, it will only get better. Feel free to reachout to me and the Apache CloudStack team on IRC or on the mailing lists.

<center>
<iframe width="800" height="450" src="http://www.youtube.com/embed/BjkGp3egv9g" frameborder="0" allowfullscreen></iframe>
</center>

