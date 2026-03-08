---
layout: post
title: "Linux System Intro"
date: 2026-03-07
---

# Introduction

This is the first of a series of posts I'll be making as I work through the The Linux Programming Interface book by Michael Kerrisk. I've worked with Linux systems over the years but have been disappointed in my lack of a real intuitive understanding of how they work under the hood. I like to know how things work in detail, and so I'm going to go through the Kerrisk text and use this blog as a place to document my notes, learnings, and experiments. This blog will expand to other areas of interest, but for now, I'll be focused on Linux. 

# The Kernel

The kernel is "the central software that manages and allocates computer resources (i.e., the CPU, RAM, and devices)". This central software makes it easier for other programs to run and use the system's resources. On Linux, the kernel executable is usually found at `/boot/vmlinuz`. On my Ubuntu Server 24.04 VM, the `vmlinuz` file is a symbolic link to `vmlinuz-6.8.0-101-generic`. 

The kernel is responsible for several tasks:
- process scheduling
- memory management
- file system provisioning
- creation and termination of processes
- access to devices
- networking
- provision of a system call API

# The Shell

The shell is a special program used by a user interactively. It interprets commands issued from the user.

# Processes

Processes are instances of executing programs. A process has 4 parts:
- text: the program instructions
- data: static variables
- heap: where programs can dynamically allocate extra memory
- stack: a piece of memory that the program uses for local variable storage and function call linkage; can expand and contract.

All processes are created from other processes. This can be done when a process calls the *fork()* system call. 

Processes can inherit the parent processes data, stack, and heap segments.

The *init* process is the special process that the kernel starts when booting. Every other process is derived from *init* located at */sbin/init*.

Daemons are long-lived processes that run in the background. These are analogous to *services* in Windows. These would run processes for things like web servers.

# Libraries

A library is a collection of functions that can be used by another program.

Static libraries
- The original kind of library on UNIX.
- Static libraries are copied into the calling program's executable binary.
- The downside to this is that the resulting binary can be bloated with all the duplicated code from the linked libraries. And similarly, there can be wasted memory when the program is executed.
- Another downside is that if a change is made to the static library, any program that wants to include the change in the library has to be rebuilt.

Shared libraries
- Shared libraries on the other hand essentially just reference the libaries at runtime.
- So disk space and memory are conserved.
- And changes to the shared library are reflected in any of the calling programs.

# Interprocess communication and synchronization

Processes can communicate with other processes in several ways.
- signals: used to show when an event occurred.
- pipes: used for transferring data between processes, particularly on the command line. The output of one program can be "piped" in as the input to the next program.
- sockets: used for transferring data from one process to another; processes can be on the same host or on different computers on the network.
- file locking: allows a process to prevent other processes from accessing a region of a file.
- message queues: used for exchanging messages
- semaphores: used for process synchronization
- shared memory: allows multiple processes to share a piece of memory.

# Signals

also known as: software interrupts. 
Signal types are identified by an integer and name: e.g. SIGTERM, SIGKILL.

Signals can be sent by the kernel, another process, or the process itself.

How a process handles the signal depends on the program and the signal. A process might ignore the signal, is killed by the signal, or be suspended until resumed by a later different signal.

Programs can choose to listen for different signal types and implement how they want to respond to those different signals.

# Threads

A process can have multiple threads of execution. Threads share the same memory, code, data, and heap. But each gets its own stack for local variables and function linkage. 

Threads can communicate via shared global variables and other IPC mechanisms. They can synchronize using different mechanisms as well such as semaphores. Theads allow for leveraging multiprocessor hardware, which is ubiquitous now.

# The /proc filesystem

The /prov filesystem is a special virtual filesystem mounted at `/proc`. This filesystem has files and directories used for showing various attributes of the processes. Attributes to a specific process are under the `/proc/{PDI}` directory where `PID` is that process's process ID. I'll create post later going into more depth on the /proc filesystem when I learn more about it.