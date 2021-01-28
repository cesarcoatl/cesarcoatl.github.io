---
layout: post
title: Configuring Jython in PyCharm
subtitle: How I struggled and eventually succeeded, so you don't have to
tags: [guide, ignition, jython, macos, pycharm]
date: 2020-10-01 19:02 -0700
last-updated: 2021-01-28 13:22 -0800
---
## Table of Contents
- [What is Jython?](#what-is-jython)
- [Why do I use Jython?](#why-do-i-use-jython)
- [Jython within Ignition](#jython-within-ignition)
- [Installing Jython on macOS](#installing-jython-on-macos)
- [Configuring Jython in PyCharm](#configuring-jython-in-pycharm)
- [Conclusion](#conclusion)
- [Sources](#sources)

### What is Jython?[^1]

Jython is a Java implementation of Python that combines expressive power with clarity. Jython is freely available for both commercial and non-commercial use and is distributed with source code under the PSF License v2. Jython is complementary to Java and is especially suited for the following tasks:

- Embedded scripting - Java programmers can add the Jython libraries to their system to allow end users to write simple or complicated scripts that add functionality to the application.
- Interactive experimentation - Jython provides an interactive interpreter that can be used to interact with Java packages or with running Java applications. This allows programmers to experiment and debug any Java system using Jython.
- Rapid application development - Python programs are typically 2-10x shorter than the equivalent Java program. This translates directly to increased programmer productivity. The seamless interaction between Python and Java allows developers to freely mix the two languages both during development and in shipping products.

### Why do I use Jython?

That's a good question, and the answer is because at work I am one of the developers for [SCADA](https://en.wikipedia.org/wiki/SCADA){:target="_blank"} applications using the [Ignition](https://inductiveautomation.com/ignition/){:target="_blank"} platform.

> **Where is scripting used?**[^2]
>
> [J]ython is used in many places in Ignition. Each location has its own events that trigger your scripts to run, and add functionality to your projects in different ways.

### Jython within Ignition

[Ignition 8.0.16](https://inductiveautomation.com/downloads/ignition/8.0.16){:target="_blank"} (the latest stable version as of the date of this post) ships with Jython 2.7.1, and Zulu11.39+16-SA (build 11.0.7+10-LTS). All of this means that we have access to both worlds; [Python 2.7](https://docs.python.org/2/){:target="_blank"}, and [Java 11](https://docs.oracle.com/en/java/javase/11/docs/api/index.html){:target="_blank"}.

And we are able to define the following function:

```python
from javax.swing import JOptionPane

def warning(message, title='Warning'):
    """Displays a message to the user in a warning style popup dialog.

    Args:
        message (str): The message to display in an warning box.
        title (str): A title for the warning box. Optional.
    """
    JOptionPane.showMessageDialog(
        None,
        message,
        title,
        JOptionPane.WARNING_MESSAGE
    )
```

And when we call it like this:

```python
warning('This one is a warning.')
```

We get the following:

![jython-warning]({{ site.url }}/assets/img/jython-warning.png){: .mx-auto.d-block :}

### Installing Jython on macOS

{: .box-error}
Do not install Jython 2.7.2 as it is not currently supported by PyCharm. See [PY-44759](https://youtrack.jetbrains.com/issue/PY-44759){:target="_blank"}.

Since I intend to set up Jython as a Project Interpreter in PyCharm, and considering the versions used by Ignition, I'll download the following:

1. Java 11.0.7 (LTS) from Azul for macOS ([.zip](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.zip){:target="_blank"}, [.dmg](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.dmg){:target="_blank"}, [.tar.gz](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.tar.gz){:target="_blank"}).
1. Jython 2.7.1
    1. Via [Homebrew](https://brew.sh/){:target="_blank"}. Just tap [coatl-dev's Homebrew tap](https://github.com/coatl-dev/homebrew-coatl-dev/){:target="_blank"}, and install `jython@2.7.1`:
    ```bash
    $ brew install coatl-dev/coatl-dev/jython@2.7.1
    ```
    Or
    ```bash
    $ brew tap coatl-dev/coatl-dev
    $ brew install jython@2.7.1
    ```
    1. Or by downloading the installer [Jython Installer v2.7.1](https://search.maven.org/artifact/org.python/jython-installer/2.7.1/jar){:target="_blank"}

Once Java has been installed, we run the following:

```bash
$ java -version
openjdk version "11.0.7" 2020-04-14 LTS
OpenJDK Runtime Environment Zulu11.39+15-CA (build 11.0.7+10-LTS)
OpenJDK 64-Bit Server VM Zulu11.39+15-CA (build 11.0.7+10-LTS, mixed mode)
$ java -jar ~/Downloads/jython-installer-2.7.1.jar
```

I decided to install it on the default location `~/jython2.7.1`, and then created a symlink.

{: .box-note}
I do not need `sudo` when creating the symlink at `/usr/local/bin`; Homebrew already took care of that.

```bash
$ cd ~/jython2.7.1/bin
$ chmod +x jython
$ cd /usr/local/bin
$ ln -s ~/jython2.7.1/bin/jython jython
```

And now I can run `jython` anywhere.

**NOTE:** There is a warning when running `jython` with Java 11, but everything works fine.

```bash
$ jython
WARNING: An illegal reflective access operation has occurred
WARNING: Illegal reflective access by org.python.core.PySystemState (file:/Users/thecesrom/jython2.7.1/jython.jar) to method java.io.Console.encoding()
WARNING: Please consider reporting this to the maintainers of org.python.core.PySystemState
WARNING: Use --illegal-access=warn to enable warnings of further illegal reflective access operations
WARNING: All illegal access operations will be denied in a future release
Jython 2.7.1 (default:0df7adb1b397, Jun 30 2017, 19:02:43) 
[OpenJDK 64-Bit Server VM (Azul Systems, Inc.)] on java11.0.7
```

To circumvent this, you must run Jython with the following flags:

```bash
$ jython -J--add-opens=java.base/java.io=ALL-UNNAMED -J--add-opens=java.base/java.lang=ALL-UNNAMED -J--add-opens=java.base/java.nio=ALL-UNNAMED -J--add-opens=java.base/sun.nio.ch=ALL-UNNAMED -J--add-opens=java.desktop/sun.awt=ALL-UNNAMED -J--add-opens=java.desktop/sun.lwawt.macosx=ALL-UNNAMED 
Jython 2.7.1 (default:0df7adb1b397, Jun 30 2017, 19:02:43) 
[OpenJDK 64-Bit Server VM (Azul Systems, Inc.)] on java11.0.7
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

### Configuring Jython in PyCharm

1. Open or create a project
1. Open **Preferences** (&#8984;+,)
1. Navigate to **Preferences > Project > Project Interpreter**
1. Click on the gear icon and select **Add...**
1. Choose **System Intepreter**
1. Select **/usr/local/bin/jython** from the **Interpreter** list, and click **OK**
1. PyCharm will name it **Jython 2.7** by default, but you can change it
1. Finally go to **Preferences > Build, Execution, Deployment > Console > Python Console** and add the following flags as **Interpreter options**
    ```
    -J--add-opens=java.base/java.io=ALL-UNNAMED -J--add-opens=java.base/java.lang=ALL-UNNAMED -J--add-opens=java.base/java.nio=ALL-UNNAMED -J--add-opens=java.base/sun.nio.ch=ALL-UNNAMED -J--add-opens=java.desktop/sun.awt=ALL-UNNAMED -J--add-opens=java.desktop/sun.lwawt.macosx=ALL-UNNAMED
    ```

To test it I will be using the [jython](https://github.com/thecesrom/Ignition/tree/jython){:target="_blank"} branch of my [Ignition](https://github.com/thecesrom/Ignition){:target="_blank"} project.

And when I run the Python Console I can do the following:

```python
>>> from java.util import Date
>>> now = Date()
>>> print now
Thu Oct 01 19:01:27 PDT 2020
>>> print type(now)
<type 'java.util.Date'>
```

And just like the example in [Jython within Ignition](#jython-within-ignition), I get the same pop-up by running the following code:

```bash
>>> import system.gui
>>> system.gui.warningBox('This one is a warning.')
```

![jython-warning]({{ site.url }}/assets/img/jython-warning.png){: .mx-auto.d-block :}

### Conclusion

And with that, my friends, you are all set.

Thanks, for reading.

### Sources
[^1]: Jython - [link](https://www.jython.org/){:target="_blank"}
[^2]: Ignition User Manual 8.0 - [link](https://docs.inductiveautomation.com/display/DOC80/Scripting#Scripting-WhereIsScriptingUsed?){:target="_blank"}