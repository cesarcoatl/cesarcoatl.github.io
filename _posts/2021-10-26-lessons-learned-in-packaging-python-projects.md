---
layout: post
title: Lessons learned in packaging Python projects
tags:
- github
- ignition
- incendium
- inductive automation
- jython
- pip
- pypi
- python
- testpypi
date: 2021-10-26 15:39 -0700
---
Recently I embarked upon a mission, and that mission was to package my [Ignition](https://github.com/thecesrom/Ignition) project, which was built based on my scripting needs, but did not make use of all System Functions.

As time passed the project grew along with my needs, but after a comment made by [RadicalRumin](https://forum.inductiveautomation.com/t/system-library-files/29773/3?u=thecesrom.git) at Inductive Automation Forum, I got the boost I needed to implement all System Functions, and created branches for each active version.

Almost a year after RadicalRumin's comment, a user by the name kgamble [commented](https://forum.inductiveautomation.com/t/ignition-repo-now-with-branches/37932/6?u=thecesrom.git) about using Ignition on Visual Studio Code, something I had not been able to accomplish. And their comment did not resonate until almost a year later.

So, a couple of months ago I started experimenting with packaging Ignition, and all that it entails. But after adding a `setup.py` [file](https://forum.inductiveautomation.com/t/ignition-repo-now-with-branches/37932/7?u=thecesrom.git), and trying `python setup.py install`, I was still not able to get code completion on Visual Studio Code.

I finally got the courage to try PyPI, so I read the ["Packaging Python Projects"](https://packaging.python.org/tutorials/packaging-projects/) tutorial, and the ["Packaging and distributing projects"](https://packaging.python.org/guides/distributing-packages-using-setuptools/) guide, and based my first `setup.py` file insipired by navdeep-G's [setup.py](https://github.com/navdeep-G/setup.py) project, psf's [black](https://github.com/psf/black/blob/HEAD/setup.py), and [requests](https://github.com/psf/requests/blob/HEAD/setup.py).

Here it is in its splendor:

```python
#!/usr/bin/env python2
# -*- coding: utf-8 -*-

"""Ignition API."""

import os
from codecs import open

from setuptools import find_packages, setup

here = os.path.abspath(os.path.dirname(__file__))

about = {}
with open(os.path.join(here, "src", "system", "__version__.py"), "r") as f:
    exec (f.read(), about)

with open("README.md", "r", "utf-8") as f:
    readme = f.read()

setup(
    name=about["__title__"],
    version=about["__version__"],
    description=about["__description__"],
    long_description=readme,
    long_description_content_type="text/markdown",
    author=about["__author__"],
    author_email=about["__author_email__"],
    python_requires=">=2.7",
    url=about["__url__"],
    packages=find_packages(where="src"),
    package_dir={"": "src"},
    license=about["__license__"],
    classifiers=[
        "Intended Audience :: Information Technology",
        "Intended Audience :: Manufacturing",
        "License :: OSI Approved :: MIT License",
        "Operating System :: MacOS :: MacOS X",
        "Operating System :: Microsoft :: Windows",
        "Operating System :: POSIX :: Linux",
        "Programming Language :: Python",
        "Programming Language :: Python :: 2 :: Only",
        "Programming Language :: Python :: 2.7",
        "Topic :: Software Development :: Libraries :: Python Modules",
        "Topic :: Software Development :: Testing :: Mocking",
    ],
)

```

Finally, I published it into PyPI as [ignition-api](https://pypi.org/project/ignition-api/) for the pure Python code, and [ignition-api-jython](https://pypi.org/project/ignition-api-jython/) for the Jython implementation.

But calamity struck. I was getting an error when trying to install the package on Jython: "ERROR: No matching distribution found for setuptools>=40.8.0". But I found that Jython 2.7.2 comes with version 41.0.1, so it didn't make sense. What was this madness?

Then I tried to install `youtube-dl` from GitHub releases, and it succeeded! What was the difference between that project and mine? It turned out, that just like [normanius](https://github.com/normanius) mentioned on an [issue](https://github.com/pypa/pip/issues/9242) they created, the culprit was the presence of `pyproject.toml`.

I was using `pyproject.toml` for configuring `black` and `pydocstyle`, but nothing else. So, an easy fix was to move all settings for `flake8`, `isort`, and `pydocstyle` into `setup.cfg` and create a workflow for checking code quality using `pylint` and `black`.

```yml
name: CI

on: [push, pull_request]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.9
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        python -m pip install pylint
        python -m pip install black
    - name: Checking the code with black
      run: |
        black --check --line-length 79 --target-version py27 src
    - name: Analysing the code with pylint
      run: |
        pylint src

```

A small diversion, [GitHub actions' starter workflow for pylint](https://github.com/actions/starter-workflows/blob/HEAD/ci/pylint.yml) is essentially [broken](https://github.com/actions/starter-workflows/issues/636). And I have a PR that should fix that: [actions#1108](https://github.com/actions/starter-workflows/pull/1108).

And that did it!

Happy ending? No.

I continued refactoring my project, and moved some duplicate code to the corresponding package: `com.inductiveautomation`. Code, code, code. Type, type, type. Problems arose.

On my ["Configuring Jython in PyCharm"]({{ site.url }}/2020/10/01/configuring-jython-in-pycharm/#installing-jython-on-macos) guide I wrote about having issues with Jython 2.7.2 when it was set as the project interpreter, but now 2.7.1 doesn't work either. No big deal, right? They'll fix both issues I've submitted, right? [PY-44759](https://youtrack.jetbrains.com/issue/PY-44759) and [PY-50491](https://youtrack.jetbrains.com/issue/PY-50491), right? Right? Maybe.

But! There's always a but. Jython fails to import modules/subpackages under `com.inductiveautomation`. See: [jython#131](https://github.com/jython/jython/issues/131). And that, my friends, is what made me pull the plug on Ignition's `jython` branch, and marking `ignition-api-jython` as **Inactive**.

Could this be fixed? I don't think so, as Jython is somewhat a graveyard at this moment, and I don't have the knowledge nor interest in working on a possible fix. But still, I decided to move the `jython` branch to its own repo ([ignition-api-jython](https://github.com/thecesrom/ignition-api-jython)) in case there is a solution in the future, or if someone is willing to fork it.

Finally, I learned about automating [publishing to PyPI via GitHub actions](https://github.com/thecesrom/Ignition/blob/HEAD/.github/workflows/pypi_upload.yml), as well as moving away from `setup.py` and use `pyproject.toml` and `setup.cfg` instead, and installing Python packages' extras and using them for `pre-commit`. But that, my friends, will be a story for another time.

Thanks for reading. And happy coding.
