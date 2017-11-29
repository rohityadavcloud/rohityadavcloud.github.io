---
layout: post
category: cloudstack
highlight: primary
title: CloudStack CloudMonkey
redirect_from: "/logs/cloudmonkey/"
---

About 2-3 weeks ago I started writing a CLI (command line interface) for [Apache CloudStack](http://cloudstack.apache.org). I researched some options and finally chose Python and cmd. Python comes preinstalled on almost all Linux distros and Mac, and cmd is a standard package in Python with which one can write a tool which can work as a command line tool and as an interactive shell interpretor. I named it `cloudmonkey` after the project's mascot.

<div class="post-image">
    <img src="/images/cloudstack/cloudmonkey-mac.png"><br><p>CloudMonkey on OSX</p>
</div>

At this time, Apache CloudStack has around 300+ restful [APIs](http://cloudstack.apache.org/api.html), and writing api handlers (autocompletion, help, request handlers etc.) for each API seemed a mammoth task at first. Marvin (the ignored robot) came to rescue. <br>

<div class="post-image">
    <img src="/images/cloudstack/cloudmonkey-ubuntu.png"><br><p>CloudMonkey on Ubuntu</p>
</div>

I grouped the apis based on their starting lowercase substring of their name, for example for the api listUsers, the substring will be list. Based on such pattern, I wrote the code so that it would group APIs based on such verbs and create handlers on the fly and add them to the shell class. The handlers are actually closures so, this way every handler is actual a dynamic function in memory enclosed by the closure generator for a verb. In the initial version, when a command was executed first time based on its verb, command class from appropriate module from cloudstackAPI would be loaded and a cache dictionary would be populated if a cache miss was hit. In later version, I wrote a cache generator which would precache all the APIs at build time to cheat on the runtime lookup overhead from O(n) to O(1). This cache would contain for each verb the api name, required params, all params and help strings. This dictionary is used for autocompletion for the verbs, the commands and their parameters, and for help strings.

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

Right now `cloudmonkey` is available as a community distribution on the [cheese shop](http://pypi.python.org/pypi/cloudmonkey/), so `pip install cloudmonkey` already! It has a [wiki](https://cwiki.apache.org/confluence/display/CLOUDSTACK/CloudStack+cloudmonkey+CLI) on building, installation and usage instructions, or watch a [screencast](http://www.youtube.com/watch?v=BjkGp3egv9g) ([transcript](http://home.apache.org/~bhaisaab/cloudstack/cloudmonkey/cloudmonkey-screencast-user-transcript.txt), [alternate link](http://home.apache.org/~bhaisaab/cloudstack/cloudmonkey/cloudmonkey-screencast-user.mov)) I made for users. As the userbase grows, it will only get better. Feel free to reachout to me or the Apache CloudStack team on our IRC or on the mailing lists.

