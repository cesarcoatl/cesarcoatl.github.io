---
layout: post
title: Let's talk about Ignition
subtitle: GitHub repo
tags:
- ignition
- jython
- python
- scada
date: 2020-11-17 18:55 -0800
---
I started working on [Ignition](https://inductiveautomation.com/ignition/){:target="_blank"} projects at work back in 2015 if my memory does not fail me, but back then my approach was the same I was taught during the 5-day core training. As demand for SCADA projects increased, I started looking for Python IDEs that would allow me to use either Python and/or Jython as the interpreter. After trying Eclipse + PyDev, PyCharm CE, and Visual Studio Code, I found that PyCharm fit my needs.

Since some of my scripts referenced functions from the `system` package from Ignition Scripting API, I was getting squiggly red lines letting me know that PyCharm couldn't find the referenced modules from `system`. So I decided I would recreate those functions as needed, and thinking that fellow Ignition developers could be facing something similar I decided to share with them the functions I had recreated, and so [Ignition mock scripts](https://github.com/thecesrom/Ignition-mock-scripts){:target="_blank"} was born back in December 2017; now archived and replaced by the [Ignition](https://github.com/thecesrom/Igniton){:target="_blank"} repo.

After a year or so, the number of modules and functions on the GitHub repo did not include all of the modules and functions of the Scripting API, but that changed when I read at the [forums](https://forum.inductiveautomation.com/t/system-library-files/29773/3){:target="_blank"} someone said that my repo hadn't been updated in a while, and that did it. I decided I would try to recreate all modules and functions from Ignition Scripting API.

Currently, Inductive Automation supports three different versions: 7.9, 8.0, and 8.1; so I reflect the same on the repo, and we have the following branches:

* main: This branch will contain all Scripting Functions from the latest release; currently 8.1.0
* 7.9: This branch contains all Scripting Functions from the 7.9 version; currently 7.9.16
* 8.0: Same as above, but for version 8.0; 8.0.17 is the latest
* jython: Same as `main`, but instead of using Python functions from the standard library it calls Java functions

And for previous versions, I release the source code for each Ignition release [here](https://github.com/thecesrom/Ignition/releases){:target="_blank"}. So instead of going back in time looking for a commit that contains the functions for the Ignition version in use, one could just go to releases and download the one they need.

Working on this project I have learned some `git`, as well as how to maintain a project that some people may find useful, and finally, it has made me more appreciative of all those maintainers, sometimes unpaid. So please consider supporting them in any way you can.

Happy coding!
