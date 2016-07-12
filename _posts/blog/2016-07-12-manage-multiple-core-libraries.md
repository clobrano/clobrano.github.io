---
layout: post
excerpt: "Multiple versions of core libraries"
categories: blog
title: Backward compatibility for applications that need old core libraries
tags: [microblogging]
published: true
date: 2016-07-12 00:00
share: true
---

This is just a reminder to myself, in case I will needed in the future.

Some days ago I was discussing this interesting problem: how to manage applications that need different versions of some core libraries (e.g. glibc).
As example, if we have an application that needs some shared library with soname `reallyneedthis.so.1`, I can package the application together with that library, but it is not that simple for core libraries that are already in the OS.

Then, a few days later, I used `LD_LIBRARY_PATH` to test a custom version of ModemManager (the system was using the wrong library ([could not find symbol X])) and I light switched on in my head. Can we use `LD_LIBRARY_PATH` for core libraries?

Unfortunately is not that easy, because it might happen that some absolute path is hard-coded at link time (e.g. ld-linux.so.2 from glibc) and there might be other problems too. Those static paths are the ones reported by "ldd".

The easiest solution would require to build the application with the new glibc (or with something like [rtldi](http://bitwagon.com/rtldi/rtldi.html)) using the --dynamic-linker flag (ELF executable). However the application can not be rebuilt, usually, and then things get trickier. One solution would be to "instruct" the linker before running the application

{% highlight bash %}
$ /path/to/new/libc/ld-linux.so.2 --library-path /path/to/lib <binary>
{% endhighlight %}

Some articles I read suggest also the *chroot* solution, but I still need to see this solution better.


