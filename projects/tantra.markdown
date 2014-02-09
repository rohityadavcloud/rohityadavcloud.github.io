---
layout: project
title: Tantra
permalink: /tantra/
---

<div class="alert alert-error">
Someday I'll complete it and write a small guide to implementing operating system kernel for hobbyist programmers.
</div>

Out of curiosity I'm trying to learn and write a small monolithic kernel for x86. It's call "Tantra" derived from __sanskrit__ phrase संचालन (sanchalan:operating) तंत्र (tantra:system).

![Tantra Screenshot](/images/projects/tantra.png)

##Booting

Custom bootloader: Reads and loads kernel in ELF format
Support for GRUB

###Initialization

GDT, LGDT, Segmentation, Enable paging
ISRs, IDT, Traps, Timers
Drivers: TTY: Console, vga, rs232, keyboard, (network, sound)

### libc-tantra
Implement ANSI C standard libs routines

##Memory Management

MMU, malloc/free (brk, sbrk)

##Process

Preemptive multitasking, tasks, scheduling and smp

##User-level environment

shell, pipes, redirectors

tools: echo, ls, cd, mkdir, rm, cat, wc, grep, more, text-editor

process: fork, exec, shutdown, reboot 

##File system and spawn

FAT/ext2 based

## Optional

###Network

Basic IPV4 stack that can telnet or ping

###Sound

Intel HD Audio or soundblaster based drivers
