---
layout: post
title: Configuring Jython in PyCharm
subtitle: How I struggled and eventually succeeded, so you don't have to
tags: [guide, ignition, jython, macos, pycharm]
date: 2020-10-01 19:02 -0700
last-updated: 2021-01-28 15:03 -0800
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

> Updated: {{ page.last-updated | date: site.date_format }}

1. Java 11
    1. Via [Homebrew](https://brew.sh/){:target="_blank"}.

    ```bash
    brew install --cask zulu11
    ```

    1. Or from Azul for macOS ([.zip](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.zip){:target="_blank"}, [.dmg](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.dmg){:target="_blank"}, [.tar.gz](https://cdn.azul.com/zulu/bin/zulu11.39.15-ca-jdk11.0.7-macosx_x64.tar.gz){:target="_blank"}).
1. Jython 2.7.1
    1. Via [Homebrew](https://brew.sh/){:target="_blank"}. Just tap [coatl-dev's Homebrew tap](https://github.com/coatl-dev/homebrew-coatl-dev/){:target="_blank"}, and install `jython@2.7.1`:

    ```bash
    brew install coatl-dev/coatl-dev/jython@2.7.1
    ```

    Or

    ```bash
    brew tap coatl-dev/coatl-dev
    brew install jython@2.7.1
    ```

    1. Or by downloading the installer [Jython Installer v2.7.1](https://search.maven.org/artifact/org.python/jython-installer/2.7.1/jar){:target="_blank"}

I decided to install both using `brew`.

First `zulu11`:

```bash
$ brew install --cask zulu11
==> Downloading https://cdn.azul.com/zulu/bin/zulu11.45.27-ca-jdk11.0.10-macosx_x64.dmg
######################################################################## 100.0%
==> Installing Cask zulu11
==> Running installer for zulu11; your password may be necessary.
Package installers may write to any location; options such as `--appdir` are ignored.
Password:
installer: Package name is Zulu 11.45+27
installer: Upgrading at base path /
installer: The upgrade was successful.
🍺  zulu11 was successfully installed!
```

Then `jython@2.7.1`:

```bash
$ brew install coatl-dev/coatl-dev/jython@2.7.1
==> Installing jython@2.7.1 from coatl-dev/coatl-dev
==> Downloading https://search.maven.org/remotecontent?filepath=org/python/jython-installer/2.7.1/jython-installer-2.7.1
==> Downloading from https://repo1.maven.org/maven2/org/python/jython-installer/2.7.1/jython-installer-2.7.1.jar
######################################################################## 100.0%
==> java -jar /Users/thecesrom/Library/Caches/Homebrew/downloads/558a886fedd7c18b1e12419bd4ab398b3ad7aaa902df4f5686ef3b695b89f2b9--jython-installer-2.7.1.jar -s -d /usr/local/Cellar/jython@2.7.1/2.7.1/libexec
🍺  /usr/local/Cellar/jython@2.7.1/2.7.1: 3,785 files, 148.7MB, built in 1 minute 22 seconds
```

**NOTE:** There is a warning when running `jython` with Java 11, but everything works fine.

```bash
$ jython
WARNING: An illegal reflective access operation has occurred
Illegal reflective access by org.python.core.PySystemState (file:/usr/local/Cellar/jython@2.7.1/2.7.1/libexec/jython.jar) to method java.io.Console.encoding()
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

    ```properties
    -J--add-opens=java.base/java.io=ALL-UNNAMED
    -J--add-opens=java.base/java.lang=ALL-UNNAMED
    -J--add-opens=java.base/java.nio=ALL-UNNAMED
    -J--add-opens=java.base/sun.nio.ch=ALL-UNNAMED
    -J--add-opens=java.desktop/sun.awt=ALL-UNNAMED
    -J--add-opens=java.desktop/sun.lwawt.macosx=ALL-UNNAMED
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

```python
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
