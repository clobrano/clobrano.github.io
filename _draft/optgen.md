The birth of the tool I want to talk about today is quite funny.
I am an happy VIM user since three years and I like to learn new stuff about it, its configuration and plug-ins, so a year ago, it happend to watch [this]() talk. (Note:Please watch it, because it's at the same time informative and really funny). There are actually several hints I took from that talk, however, the main one regarding this post was how easy is in VIM to make your own snippets without any additional plugin. I've never been a frequent snippet user, but I wrote tons of bash script for my work and all of them:

1. lacked of header documentation: what they do and why
2. used positional arguments with no description at all
3. adjusted all the time in a fast manner, so if any documentation was there, was very likely out of date

the result of this was that most of them were useless after some time, so I tryied to use a snippet to generate some code for **getopts** and at least organize the command line arguments in a better way. This has improved by a lot the organization of my code and put me on the right direction. Snippet was fine but not enough, because I was still missing documentation. To fix that I took a new hint from a blog post (my apologies, I do not remember which one anymore). The idea relies on two steps:

1. prepend useful comments inside the script with a specific and easy tag (like "##")
2. write a function that parse the script itself looking for those tag and write them on stdout as *help message*.

Cool, for like one month I took this version of the snippet, improving a little bit more the quality of my scripts. However that as not enough: why generate documentation from code, when I can (could) generate code from documentation? I already knew [docopt](), which is great, and I assume other similar tools exist. Docopt itself has a *bash* version, but I didn't want to depend on an external library every time I need to run a bash script. This is perfectly fine in **python**, where you are expected to have such dependencies and also tools to get them easily, but for bash script I like to be as independent as possible. Every script should be enough alone.


