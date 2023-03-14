---
layout: post
title: Installing pyenv and managing Python versions in Ubuntu
subtitle: From macOS to Ubuntu, Pt. 2
tags:
- github
- linux
- macos
- pyenv
- python
- ubuntu
date: 2023-03-10 14:35 -0800
---
Read **Part 1** [here]({{ site.url }}/2023/02/18/taking-a-ten-year-old-macbook-air-out-for-one-last-ride-with-ubuntu/).

Coming from macOS into Ubuntu required some "getting used to things". Like using `apt` (and
[`nala`](https://gitlab.com/volian/nala/-/wikis/Installation)), but still I installed Homebrew for Linux, as most of
the utilities are not up to date, and being more used to Homebrew it only made sense to me.

So, as usual, I started setting up my development environment, which requires Python 2.7 and Python 3.10, and for
managing multiple versions of Python `pyenv` is my go-to over similar tools like `asdf`.

Installing Python 2.7 was a breeze, but I did run into some issues when installing Python 3.10 mainly because `pyenv`'s
`python-build` script relies in Homebrew only when running on macOS, which might be an opportunity for creating a
feature request.

Even after getting most of the errors listed in `pyenv`'s
["Common build problems"](https://github.com/pyenv/pyenv/wiki/Common-build-problems) wiki page it dawned upon me that I
was approaching this the wrong way. If `pyenv`'s `python-build` script can't do it, I can build Python following
Python's guide!

Although I did notice some issues with the instructions in there, and so PR
[python/devguide#1052](https://github.com/python/devguide/pull/1052) was created.

And this is how one can build Python in Ubuntu and macOS with Homebrew.

Using Python's [Setup and Building](https://devguide.python.org/getting-started/setup-building/), and the script I
generated using [Build, compile and install Python from source code](https://www.build-python-from-source.com/) I had
success doing the following.

{: .box-error}
I'm sure that seasoned Linux and Ubuntu users might not even consider Homebrew as an alternative to their built-in
package manager, but `brew` is what I know.

First install all required packages with `brew`:

```sh
brew install pkg-config openssl@1.1 xz gdbm tcl-tk wget pyenv
```

And for building Python 3.10:

```sh
$ cd /tmp/
$ wget https://www.python.org/ftp/python/3.10.10/Python-3.10.10.tgz
$ tar xzf Python-3.10.10.tgz
$ cd Python-3.10.10
$ CFLAGS="-I$(brew --prefix gdbm)/include -I$(brew --prefix xz)/include"  \
  LDFLAGS="-L$(brew --prefix gdbm)/lib -I$(brew --prefix xz)/lib" \
  PKG_CONFIG_PATH="$(brew --prefix tcl-tk)/lib/pkgconfig" \
  ./configure --with-openssl="$(brew --prefix openssl@1.1)" \
          --prefix="${PYENV_ROOT}/versions/3.10.10/"
$ make --silent
$ make altinstall
```

Finally add symlinks:

```sh
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/2to3-3.10            ${PYENV_ROOT}/versions/3.10.10/bin/2to3
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/idle3.10             ${PYENV_ROOT}/versions/3.10.10/bin/idle
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/pip3.10              ${PYENV_ROOT}/versions/3.10.10/bin/pip
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/pip3.10              ${PYENV_ROOT}/versions/3.10.10/bin/pip3
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/pydoc3.10            ${PYENV_ROOT}/versions/3.10.10/bin/pydoc
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/python3.10           ${PYENV_ROOT}/versions/3.10.10/bin/python
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/python3.10-config    ${PYENV_ROOT}/versions/3.10.10/bin/python-config
ln -s ${PYENV_ROOT}/versions/3.10.10/bin/python3.10           ${PYENV_ROOT}/versions/3.10.10/bin/python3
```

And just like that I was ready to go.

Thanks for reading, and happy building!
