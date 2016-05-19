---
layout: post
title: "Solve unresolved external symbol"
excerpt: Which library provides a given external symbol?
tags: [microblogging]
category: blog
date: 2016-05-19
image:
  teaser:
  feature:
  credit:
published: true
share: true
---

The last months I've been very busy testing the integration between modems, ModemManager and client's somehow-custom Ubuntu images, so I often had to install new versions of software trying to avoid breaking dependencies. Well, at least once I didn't avoid that and the software crashed with the well known message "Could not find symbol, etc. etc.".

Using **ldd** I found that all the links to libraries were satisfied, so the problem was that one of the those libraries was not providing the expected symbol, but how to find that? Well, I had a system where everything was working fine, so the solution was easy: look inside each library for the missing symbol... Is there a way to **automate** this? Yes!

Let's see what my problem's data where:

* the name of the missing symbol
* the location of the libraries
* a system where the software was working fine

the toolkit

* **nm** is the tool for listing all the symbols
* **find** is the tool to iterate over the libraries automatically
* **grep** is the tool to check for the symbol name.

The solution is this:

{% highlight bash %}
$ find /lib/location "libraries list" -exec nm --print-file-name {} \; | grep <symbol-name>
{% endhighlight %}

the exec part is the main dish: I'm iterating over a list of files (find replaces "{} \;" with the name of a file), so I need the nm flag _--print-file-name_ so that if grep finds the symbol name, it will print also the library that contains it.

So, basically, running this script in the working system, I found that there was a mismatch in one of the libraries version.
