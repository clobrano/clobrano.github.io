---
layout: post
title: Python, you've got an e-mail
excerpt: "Python company's e-mail notifier"
categories: articles
tags: [automation, python]
date: 2015-04-27 00:00
image:
    teaser: python-email2.jpg
    feature: python-email2.jpg
published: true
share: true
---


Ok, you landed in a new IT company and you are very excited to start working.
You have got an **e-mail** address, a **calendar** for meetings, deadlines, planning, holidays (yeah) and very likely an **istant messaging** system and all is under Microsoft Exchange<a rel="nofollow" href="#footnote1" id="ref_footnote1"><sup>1</sup></a>, however you are a Linux developer and you have to (or you just prefer to) work with a GNU/Linux OS, so the question is: **what e-mail client is OK for Linux**?

I actually try and use lot of desktop e-mail clients. [**Thunderbird**](https://www.mozilla.org/en-US/thunderbird/) (with [Lightning](https://www.mozilla.org/en-US/projects/calendar/) for calendar) is probably the best, but it is huge in memory consumption. [**Evolution**](https://wiki.gnome.org/Apps/Evolution) is my preferred, above all for the clean and efficient interface, but is not always easy to configure and quite slow at startup when it tries to syncronize your e-mails. [**KMail**](https://userbase.kde.org/KMail) is nice as well as Evolution (maybe even better), but I mostly run Gtk windows environment so I prefer not to download all its KDE dependencies.

How not to mention also that if you choose a "custom" client **the responsility** for each problem (e.g. e-mails not received on time, impossibility to send important e-mails...) **is yours**<a rel="nofollow" href="#footnote2" id="ref_footnote2"><sup>2</sup></a>.

The query <u>linux email client exchange 2010</u> (at 12:32 PM of Saturday, April 25, 2015 (GMT+2)) returns more than 1 Millions results, so I am not about to talk about the best e-mail client and their configuration today. I just want to say that **you may not need a desktop client**. Luckily the Outlook web app is perfectly fine for most of my needs and more important the things that it does not provide are basicaly the same you won't have with any Linux desktop client anyway.

The only important thing here is **getting notified about new e-mails**. Again, there are some solution ready to be installed, but apart from the fact that some of them just does not work<a rel="nofollow" href="#footnote3" id="ref_footnote3"><sup>3</sup></a>, it is a lot funnier trying something on my own.

I had got no much time to spend developing a new notifier, so I choose to write it in Python, that provides the [imaplib](https://docs.python.org/2/library/imaplib.html) module to manage IMAP4 protocol.

The basic steps are the following:

Connection
----------

Better if through SSL like this

{% highlight python %}
import getpass
import imaplib

M = imaplib.IMAP4_SSL (EMAIL_IMAP_SERVER)
{% endhighlight %}

Login
-----

Here we have some options. **Password can not be revealed**, so we can provide a **crypted file with the password** or use [**getpass**](https://docs.python.org/2/library/getpass.html) Python module that provide a <u>prompt for the user without echoing</u> (that is, no one can see what you are typing).

{% highlight python%}
password = getpass.getpass ()
rv, data = M.login(EMAIL_ACCOUNT, password)
{% endhighlight %}

{% highlight python%}
{% endhighlight %}

Get your data
-------------

I was tempted to build an entire new e-mail client, but let's start with little things: the **number of unseen e-mail from the inbox**.

* Select a *mailbox*. My account has a mailbox called *INBOX* for example. As you can see in the code above, the library functions returns a tuple composed by a sort of status `rv` (it is a string, like 'OK') and the actual `data`.

    {% highlight python %}
    rv, data = M.select('INBOX')
    {% endhighlight %}

* If in the previous step `rv == 'OK'`, you can ask the status of the choosen mailbox (I went through some try and error to understand how query my e-mail).

    {% highlight python %}
    rv, data = M.status('INBOX', '(UNSEEN)')
    {% endhighlight %}

* The `status` returned in the previous step is verbose (like: 'Unseen email: 10') so I used regular expression to extract just a number.

    {% highlight python %}
    unseen = re.search('[0-9]+', data[0]).group(0)
    {% endhighlight %}

Notify
------

This is simple enough, using **pynotify** module which is a python binding for libnotify.

{% highlight python %}
def signal(message):
	if notify == 'on':
		message += ' {0}'.format(datetime.now())
		pynotify.init('PyEmail')
		notification = pynotify.Notification('PyEmail', message)
		notification.show()
{% endhighlight %}


Conclusion
----------

There are other things to be considered, like a *mainloop*, *exceptions* and provide command-line arguments (for which I used the excellent module [docopt](http://docopt.org/)). For all those things here is the link of the [complete project](https://github.com/clobrano/imap-email-checker) on github.


#### Notes

1. Lately some companies are providing alternatives (like [Google Apps](https://www.google.com/work/apps/business/) and there are mixed solutions with e-mail from a source an IM from another), but in most instances you will work with Microsoft Exchange. I am a Linux guy, but I won't say nothing against this choice. It just works and when it does not there is an helpdesk to call and that is perfectly fine for most of the people that works in every company<a rel="nofollow" href="#ref_footnote1" id="footnote1">[↩]</a>.
2. Of course you are a Linux developer, so you can manage responsability :), but it was something I just had to say<a rel="nofollow" href="#ref_footnote2" id="footnote2">[↩]</a>.
3. See above for my idea about things you have to struggle with v.s. things that just works<a rel="nofollow" href="#ref_footnote3" id="footnote3">[↩]</a>.

