---
layout: post
title: Working with pyenv and jEnv on macOS
tags:
- guide
- homebrew
- jython
- macos
- pyenv
- python
date: 2020-09-21 13:02 -0700
---
## Table of Contents
- [Introduction](#introduction)
- [Pre-requisites](#pre-requisites)
- [Why penv?](#why-pyenv)
- [Installing pyenv](#installing-pyenv)
    - [Installing Python 3.8](#installing-python-38)
    - [Installing Python 2.7](#installing-python-27)
    - [Setting a global version of Python](#setting-a-global-version-of-python)
- [Installing Jython](#installing-jython)
    - [Installing jEnv](#installing-jenv)
    - [Tapping adoptopenjdk/openjdk](#tapping-adoptopenjdkopenjdk)
    - [Installing a JDK](#installing-a-jdk)
    - [jenv doctor and add](#jenv-doctor-and-add)
    - [Setting a global version of Java](#setting-a-global-version-of-java)
    - [Verify Java version](#verify-java-version)
    - [Testing Java](#testing-java)
    - [Install Jython](#install-jython)
    - [Testing Jython](#testing-jython)
- [Success!](#success)
- [Sources](#sources)

## Introduction
This is not meant to be a detailed guide on the many ways you could install Homebrew, pyenv, jEnv, and Jython, but a look into what I did to get my environment set up for my personal projects.

## Pre-requisites
Xcode Command Line Tools
```bash
xcode-select --install
```

Homebrew
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

Let's begin.

## Why pyenv?

In my personal experience, working with multiple versions of Python in Windows is as easy as downloading as many versions as you wish or need, and drop them somewhere in your C drive. For some reason that seems easier than managing multiple versions of Python in macOS.

This is where `pyenv` comes in.

> pyenv lets you easily switch between multiple versions of Python. It's simple, unobtrusive, and follows the UNIX tradition of single-purpose tools that do one thing well.

Before [breathing new life into my MacBook Air]({{ page.previous.url | relative_url }}), I had already read about pyenv in a Real Python article titled ["Managing Multiple Python Versions With pyenv."](https://realpython.com/intro-to-pyenv/){:target="_blank"} So following the steps from that guide, and pyenv's own guide on using [Homebrew on macOS](https://github.com/pyenv/pyenv#homebrew-on-macos){:target="_blank"} I got my system ready for action.

## Installing pyenv

Install dependencies
```bash
$ brew install openssl readline sqlite3 xz zlib
```

Install pyenv
```bash
$ brew install pyenv
```

Load `pyenv` automatically by running the following command to add it to your shell
```bash
$ echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.bash_profile
```

Restart shell
```bash
$ exec "$SHELL"
```

Next, I'll install specific Python versions.

### Installing Python 3.8

First, let's get a list of all 3.8 versions:
```bash
$ pyenv install --list | grep " 3\.8"
  3.8.0
  3.8-dev
  3.8.1
  3.8.2
  3.8.3
  3.8.4
  3.8.5
```

Installing the latest 3.8 release.

```bash
$ pyenv install 3.8.5
```

### Installing Python 2.7

{: .box-info}
For the [Ignition](https://github.com/thecesrom/Ignition/){:target="_blank"} project I maintain, both Python 2.7.18 or Jython 2.7.1 are required.

So, first, I will install Python 2.7.18.


```bash
$ pyenv install --list | grep " 2\.7"
  2.7.0
  2.7-dev
  2.7.1
  2.7.2
  2.7.3
  2.7.4
  2.7.5
  2.7.6
  2.7.7
  2.7.8
  2.7.9
  2.7.10
  2.7.11
  2.7.12
  2.7.13
  2.7.14
  2.7.15
  2.7.16
  2.7.17
  2.7.18
```

{: .box-note}
Python 2.7 reached its EOL on January 1, 2020. See: [Sunsetting Python 2](https://www.python.org/doc/sunset-python-2/){:target="_blank"}

Installing the final 2.7 version:

```bash
$ pyenv install 2.7.18
```

### Setting a global version of Python

Run `pyenv versions` to list all Python versions known to pyenv; an asterisk will be shown next to the currently active version.

```bash
$ pyenv versions
* system (set by /Users/thecesrom/.pyenv/version)
  2.7.18
  3.8.5
```

Run `pyenv global <version>` to set the global version of Python to be used in all shells.

```bash
$ pyenv global 3.8.5
```

Verify your selection by running `pyenv versions`.

```bash
$ pyenv versions
  system
  2.7.18
* 3.8.5 (set by /Users/thecesrom/.pyenv/version)
```

Alternatively, you could [specify multiple versions as global at once](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-global-advanced){:target="_blank"}.

## Installing Jython

Before we can install Jython, first we must install Java. So here I will show you how to do it with `jenv`.

But this is a little more involved than `pyenv`, so tag along!

### Installing jEnv

On macOS, the simpler recommended way to install is using [Homebrew](https://brew.sh/){:target="_blank"}.
```bash
$ brew install jenv
```

Adding it to Shell
```bash
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
$ echo 'if which jenv > /dev/null; then eval "$(jenv init -)"; fi' >> ~/.zshrc
```

### Tapping adoptopenjdk/openjdk

To stay with a specific major release, activate the AdoptOpenJDK tap with `brew tap` and then install the desired version with `brew cask install`:

```bash
$ brew tap adoptopenjdk/openjdk
```

Then run `brew search jdk` to list all possible options:

```bash
$ brew search jdk
==> Formulae
openjdk                                                                        openjdk@11
==> Casks
adoptopenjdk                           adoptopenjdk11-openj9-jre-large        adoptopenjdk13-openj9-jre              adoptopenjdk8
adoptopenjdk-jre                       adoptopenjdk11-openj9-large            adoptopenjdk13-openj9-jre-large        adoptopenjdk8-jre
adoptopenjdk-openj9                    adoptopenjdk12                         adoptopenjdk13-openj9-large            adoptopenjdk8-openj9
adoptopenjdk-openj9-jre                adoptopenjdk12-jre                     adoptopenjdk14                         adoptopenjdk8-openj9-jre
adoptopenjdk-openj9-jre-large          adoptopenjdk12-openj9                  adoptopenjdk14-jre                     adoptopenjdk8-openj9-jre-large
adoptopenjdk-openj9-large              adoptopenjdk12-openj9-jre              adoptopenjdk14-openj9                  adoptopenjdk8-openj9-large
adoptopenjdk10                         adoptopenjdk12-openj9-jre-large        adoptopenjdk14-openj9-jre              adoptopenjdk9
adoptopenjdk11                         adoptopenjdk12-openj9-large            adoptopenjdk14-openj9-jre-large        jdk-mission-control
adoptopenjdk11-jre                     adoptopenjdk13                         adoptopenjdk14-openj9-large            oracle-jdk
adoptopenjdk11-openj9                  adoptopenjdk13-jre                     adoptopenjdk15                         oracle-jdk-javadoc
adoptopenjdk11-openj9-jre              adoptopenjdk13-openj9                  adoptopenjdk15-jre                     sapmachine-jdk
```

### Installing a JDK

In my case, I will install JDK 11, so I run the following command:

```bash
$ brew cask install adoptopenjdk11
🍺  adoptopenjdk11 was successfully installed!
```

### jenv doctor and add

To verify `jenv` was installed, run `jenv doctor`.

```bash
$ jenv doctor
[OK]    JAVA_HOME variable probably set by jenv PROMPT
[ERROR] Java binary in path is not in the jenv shims.
[ERROR] Please check your path, or try using /path/to/java/home is not a valid path to java installation.
        PATH : /Users/thecesrom/.pyenv/shims:/Users/thecesrom/.rbenv/shims:/Users/thecesrom/.jenv/bin:/usr/local/opt/jenv/shims:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/Library/Apple/usr/bin
[OK]    Jenv is correctly loaded
```

To make sure `JAVA_HOME` is set, make sure to enable the `export` plugin:

```bash
$ jenv enable-plugin export
$ exec $SHELL -l
```

And, finally, we'll add the Java home directory using `jenv` so that it shows up in our `jenv versions` command:

```bash
$ jenv add /Library/Java/JavaVirtualMachines/adoptopenjdk-11.jdk/Contents/Home/
openjdk64-11.0.8 added
11.0.8 added
11.0 added
11 added
```

To verify run `jenv versions`:

```bash
$ jenv versions
* system (set by /usr/local/opt/jenv/version)
  11
  11.0
  11.0.8
  openjdk64-11.0.8
```

### Setting a global version of Java

Just as with `rbenv` and `pyenv`, we can set `global` or `local` versions we wish to use.

So to set a global version, run `jenv global <version>`:

```bash
$ jenv global 11.0.8
$ jenv versions
  system
  11
  11.0
* 11.0.8 (set by /usr/local/opt/jenv/version)
  openjdk64-11.0.8
```

### Verify Java version

To verify the Java version you're running by default, run `java --version`:

```bash
$ java --version 
openjdk 11.0.8 2020-07-14
OpenJDK Runtime Environment AdoptOpenJDK (build 11.0.8+10)
OpenJDK 64-Bit Server VM AdoptOpenJDK (build 11.0.8+10, mixed mode)
```

And `which java` to locate `java` in your path:

```bash
$ which java
/usr/local/opt/jenv/shims/java
```

### Testing Java

To test our Java set up, we will create a simple "Hello, World!" application.

First, create `HelloWorld.java`:
```bash
$ mkdir HelloWorld && cd HelloWorld
$ nano HelloWorld.java
```

Paste the following code:
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Finally, compile and run:
```bash
$ javac HelloWorld.java
$ java HelloWorld
Hello, World!
```

Once this has been successfully tested, let's install Jython.

### Install Jython

First, let's list all Jython versions available to `pyenv` by running the following command:

```bash
$ pyenv install --list | grep "jython"
  jython-dev
  jython-2.5.0
  jython-2.5-dev
  jython-2.5.1
  jython-2.5.2
  jython-2.5.3
  jython-2.5.4-rc1
  jython-2.7.0
  jython-2.7.1
```

Let's install version 2.7.1:

```bash
$ pyenv install jython-2.7.1
```

Check all installed versions:

```bash
$ pyenv versions
  system
  2.7.18
* 3.8.5 (set by /Users/thecesrom/.pyenv/version)
  jython-2.7.1
```

### Testing Jython

To test our Jython set up, we first must set Jython locally, since we already have selected 3.8.5 as `global`.

Let's use Jython locally by running the following commands:

```bash
$ mkdir ~/Jython
$ cd ~/Jython
$ pyenv local jython-2.7.1
$ pyenv versions
  system
  2.7.18
  3.8.5
* jython-2.7.1 (set by /Users/thecesrom/Jython/.python-version)
```

Then, we create `JOptionPane.py` with the following code:

```python
from javax.swing import JLabel, JOptionPane, JPanel, JTextField


def show_confirm():
    options = ['Yes', 'No', 'Cancel']
    choice = JOptionPane.showOptionDialog(
        None,
        'Are you sure?',
        'Confirm',
        JOptionPane.YES_NO_CANCEL_OPTION,
        JOptionPane.QUESTION_MESSAGE,
        None,
        options,
        options[0]
    )
    return (
        not bool(choice)
        if choice in [JOptionPane.YES_OPTION, JOptionPane.NO_OPTION]
        else None
    )


def show_error():
    JOptionPane.showMessageDialog(
        None,
        'An error occurred!',
        'Error',
        JOptionPane.ERROR_MESSAGE
    )


def show_info():
    JOptionPane.showMessageDialog(
        None,
        'Important information.',
        'Information',
        JOptionPane.INFORMATION_MESSAGE
    )


def show_input():
    options = ['OK', 'Cancel']
    panel = JPanel()
    label = JLabel('{}: '.format('Enter some text'))
    panel.add(label)
    text_field = JTextField(25)
    panel.add(text_field)
    choice = JOptionPane.showOptionDialog(
        None,
        panel,
        'Input',
        JOptionPane.OK_CANCEL_OPTION,
        JOptionPane.PLAIN_MESSAGE,
        None,
        options,
        options[0]
    )
    return(text_field.getText()
           if choice == JOptionPane.OK_OPTION
           else None)


def show_warning():
    JOptionPane.showMessageDialog(
        None,
        'This one is a warning.',
        'Warning',
        JOptionPane.WARNING_MESSAGE
    )


if __name__ == '__main__':
    infinite = True
    while infinite:
        print 'Testing JOptionPane using Jython'
        print 'Samples:'
        print '\t1. Confirm'
        print '\t2. Error'
        print '\t3. Info'
        print '\t4. Input'
        print '\t5. Warning'
        print '\t6. Quit'
        option = input('Select a sample: ')
        if option == 1:
            choice = show_confirm()
            print 'User selected: {}'.format(choice)
        elif option == 2:
            show_error()
        elif option == 3:
            show_info()
        elif option == 4:
            text = show_input()
            print 'User provided {}'.format(text)
        elif option == 5:
            show_warning()
        else:
            infinite = False
```

Finally, run your code with the following command:

```bash
$ jython JOptionPane.py
Testing JOptionPane using Jython
Samples:
	1. Confirm
	2. Error
	3. Info
	4. Input
	5. Warning
	6. Quit
Select a sample: 5
```

![jython-warning]({{ site.url }}/assets/img/jython-warning.png){: .mx-auto.d-block :}

## Success!

And by doing all of the above I have completed setting up my computer for working on my projects.

Happy coding!

## Sources:
- pyenv :: Modern Python Developer's Toolkit - [https://pycon.switowski.com/02-packages/pyenv/](https://pycon.switowski.com/02-packages/pyenv/){:target="_blank"}
- pyenv/pyenv: Simple Python version management - [https://github.com/pyenv/pyenv](https://github.com/pyenv/pyenv){:target="_blank"}
- Managing Multiple Python Versions With pyenv - [https://realpython.com/intro-to-pyenv/](https://realpython.com/intro-to-pyenv/){:target="_blank"}
- jEnv - Manage your Java environment - [https://www.jenv.be/](https://www.jenv.be/){:target="_blank"}
- AdoptOpenJDK - Open source, prebuilt OpenJDK binaries - [https://adoptopenjdk.net/](https://adoptopenjdk.net/)
- Jython - [https://www.jython.org/](https://www.jython.org/){:target="_blank"}