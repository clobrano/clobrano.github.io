---
layout: post
title: "Testing: Library, tell me what I want to hear!"
excerpt: "Isolate your code from the library it uses during Unittests"
categories: articles
tags: [C/C++, testing]
date: 2015-05-04 00:00
image:
  teaser: listen.jpg
  feature: listen.jpg
  credit: http://kingstheology.org
published: true
share: true
---


Programming is an activity that presents often the same problems, so that as the time passes you can see if you are improving your experience or not.

A couple of years ago I was working on a daemon that received information about hardware devices connected to the system using a open source library.
The software relied on that library to get all sort of information about the kind of device and do other stuff accordingly and that was cool and fine.
**Testing** the piece of code that used that information **was not easy** however, since it had to be "stimulated" with real devices so that I could test the behavior only with the devices in my possession.
Obviously I tought about simulating the library, but I only knew about the [LD_PRELOAD](http://man7.org/linux/man-pages/man8/ld.so.8.html) "trick" at that time and that meant to implement more functions than I wanted to. Also sometimes I only wanted to change the normal library behavior, not implement it from scratch.

Few weeks ago I went through the same problem, but this time my research stubled upon a better solution (at least if you are developing in a GNU/Linux environment): the **--wrap** GNU/Linux linker flag.

I first read about this option on [this](https://lwn.net/Articles/558106/) page<a rel="nofollow" href="#footnote1" id="ref_footnote1"><sup>1</sup></a> (towards the end of the page) and a new world opened in front of me.

[here](ftp://ftp.gnu.org/old-gnu/Manuals/ld-2.9.1/html_mono/ld.html) is the complete description of all the Linker's flags, while *--wrap* only is reported below:


![wrap-sym-def](/images/2015-05-04/wrap-sym-definition.png)


How does it work? Let's say we have a source *testcode.c* like this

{% highlight c%}
#include <stdio.h>
#include "libtest.h"

int main (void)
{
    int res = ultimate_question_of_life_universe_and_everything ();

    printf ("The response is %d\n", res);

    return 0;
}
{% endhighlight %}

and that the function *ultimate_question_of_life_universe_and_everything* is provided by an external library **libtest.so** you have no power over (and you do not need to test, too).

That means *testcode* binary has got an **Undefined (U)** symbol *ultimate_question_of_life_universe_and_everything*, that will be provided by the library at runtime.


![nm_testcode](/images/2015-05-04/testcode_sym_0.png)

The function is implemented (for the sake of this test) in the following way.

{% highlight c%}
int ultimate_question_of_life_universe_and_everything (void)
{
    printf ("Asking for '%s'\n", __FUNCTION__);
    return 42;
}
{% endhighlight %}

Compiling this code with the following line and running the binary we have:

	gcc -g -Wall -c testcode.c
	gcc -o testcode testcode.o -L./ -ltest

![run_testcode_real](/images/2015-05-04/testcode_run_real.png)

Now, I want to **provide my own implementation** of the function, so I wrote *__wrap_ultimate_question_of_life_universe_and_everything* in the same source file *testcode.c*<a rel="nofollow" href="#footnote2" id="ref_footnote2"><sup>2</sup></a>

{% highlight c %}
int __wrap_ultimate_question_of_life_universe_and_everything (void)
{
    printf ("Asking for '%s'\n", __FUNCTION__);
    return 33;
}
{% endhighlight %}

The **naming convention is important**. All the wrapped function must have the same name of the orginal functions preceded by *__wrap_*.
Also I had to **change how I compile the code** telling the GNU/Linker to replace every function named `SYMBOL` with a function named `__wrap_SYMBOL` using `-Wl,--wrap=symbol` flag

	gcc -Wl,--wrap=ultimate_question_of_life_universe_and_everything -o testcode testcode.o -L./ -ltest

## First test: run the code

![run_testcode_fake](/images/2015-05-04/testcode_run_fake.png)

Everything went fine. The function called is my *__wrap* version, also the returned value is different, but **there is more**.

## Second test: look at the symbols

Using `nm` to look at the symbols in `testcode` we can see that *ultimate_question_of_life_universe_and_everything* symbol is disappeard, replaced by my `__wrap` version, which is in fact **Defined (T)**.

![nm_testcode](/images/2015-05-04/testcode_sym_1.png)


## Conclusions

To summing up, I can now better isolate the behavior of my code during tests, providing my own controlled versions of its interfaces towards other libraries. I do not need to implement all the external libary's symbols, just the ones I need. Finally **I can even use the original implementation** calling a function name *__real_symbol*, so I can just modify the real behavior of the library to test situations hard to reproduce in a real environment.

### Notes

1. The page is about **Cmocka** C testing framework, which I found really good, by the way<a rel="nofollow" href="#ref_footnote1" id="footnote1">[↩]</a>.
2. This is only for this article. I usually wrote a new source code with all the *wrapped* functions compiled agains the test code with the --wrap flag<a rel="nofollow" href="#ref_footnote2" id="footnote2">[↩]</a>.

