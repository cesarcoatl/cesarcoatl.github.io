---
layout: post
title: ignition-api package and typing
tags:
- ignition
- inductive automation
- mypy
- pip
- pypi
- python
- typing
date: 2021-12-10 17:20 -0800
---
Many things have changed since my [last post]({{ site.url }}/2021/10/26/lessons-learned-in-packaging-python-projects), and I will do my best to do a count of the highlights.

## The return of `setup.py`

In that last post I briefly mentioned that I was moving away from `setup.py` onto `setup.cfg` and `pyproject.toml`, but when I wanted to install `ignition-api` on my Development machine as [`--editable`](https://pip.pypa.io/en/latest/cli/pip_install/#editable-installs) I realized that `setup.py` is needed to install it that way on Python 2.

Otherwise, you might get the following error:

```bash
$ python2 -m pip install --editable path/to/SomeProject
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
ERROR: File "setup.py" not found. Directory cannot be installed in editable mode: path/to/SomeProject
(A "pyproject.toml" file was found, but editable mode currently requires a setup.py based build.)
```

If you also decided to delete your `setup.py` file and move everything to `setup.cfg`, `pip` requiring a `setup.py` file in order to install your project in editable mode does not mean that you must move everything back to `setup.py`. You could keep all the static information in `setup.cfg`, and your `setup.py` file could be as simple as this:

```python
#!/usr/bin/env python

"""Some Project."""

from setuptools import setup

setup()

```

And just by doing that, `pip install` won't complain.

```bash
python2 -m pip install --editable path/to/SomeProject
DEPRECATION: Python 2.7 reached the end of its life on January 1st, 2020. Please upgrade your Python as Python 2.7 is no longer maintained. pip 21.0 will drop support for Python 2.7 in January 2021. More details about Python 2 support in pip can be found at https://pip.pypa.io/en/latest/development/release-process/#python-2-support pip 21.0 will remove support for this functionality.
Obtaining file:///path/to/SomeProject
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
    Preparing wheel metadata ... done
Installing collected packages: SomeProject
  Running setup.py develop for SomeProject
Successfully installed SomeProject
```

## `typing`, and Python 2.7.18

After I created the [`ignition-api`](https://pypi.org/project/ignition-api/) package I noticed that in PyCharm I was being warned if I was passing an unexpected argument type on my function calls, which made me realize that PyCharm was inspecting the function's docstring to determine expected types, as well as code-completion; unlike Visual Studio Code's language server for Python called [Pylance](https://devblogs.microsoft.com/python/announcing-pylance-fast-feature-rich-language-support-for-python-in-visual-studio-code/).

So, instead of adding type hints to "all" functions in Ignition, first I tried to check if there was something already in place for Visual Studio Code. How? Tweeting, of course.

>Feature request: analyze docstrings to determine argument and return types additional to type-hinting.
>
>Currently, the only way that I can let [@code](https://twitter.com/code?ref_src=twsrc%5Etfw) know about the argument type and get proper code completion is by adding type hints for [#Python2](https://twitter.com/hashtag/Python2?src=hash&amp;ref_src=twsrc%5Etfw) code.
>
>[@savostrowski](https://twitter.com/savostrowski?ref_src=twsrc%5Etfw)
>
>>— César Román (@thecesrom) [October 4, 2021](https://twitter.com/thecesrom/status/1444815763597000705?ref_src=twsrc%5Etfw)
>>
>>Exhibits A and B. No code completion vs Code completion after type hinting.
>>
>>— César Román (@thecesrom) [October 4, 2021](https://twitter.com/thecesrom/status/1444825426749132802?ref_src=twsrc%5Etfw)
>>
>>>We have an open feature request for grabbing type hints via docstrings for Python 3 code. Feel free to vote on it, if you'd like!
>>>
>>><https://github.com/microsoft/pylance-release/issues/854>
>>>
>>>Given that Python 2 has reached end of life, we have no plans to support it officially or improve its experience.
>>>
>>>— Savannah Ostrowski (@savostrowski) [October 4, 2021](https://twitter.com/savostrowski/status/1444841439079325702?ref_src=twsrc%5Etfw)

A month later I was still debating if I should add type hints or not.

>Type hinting or not?
>
>[#python](https://twitter.com/hashtag/python?src=hash&amp;ref_src=twsrc%5Etfw) [#python2](https://twitter.com/hashtag/python2?src=hash&amp;ref_src=twsrc%5Etfw) [#vscode](https://twitter.com/hashtag/vscode?src=hash&amp;ref_src=twsrc%5Etfw) [#pylance](https://twitter.com/hashtag/pylance?src=hash&amp;ref_src=twsrc%5Etfw)
>
>—César Román (@thecesrom) [November 11, 2021](https://twitter.com/thecesrom/status/1458595850075074563?ref_src=twsrc%5Etfw)

The main motivation behind this was to get a similar experience across IDEs. So, before another month went by I decided to add type hints to all [System Functions](https://docs.inductiveautomation.com/display/DOC81/System+Functions).

But I was aware that doing that would put me in a tough position deciding which Python versions I would support. A few months ago I decided that `ignition-api` would be allowed to be installed in Python versions greater or equal to `2.7`; this meant that I should add `try`-`except` blocks when attempting to import `__builtin__` as it was renamed to `builtins` in Python 3, as well as the deprecation of `basestring` and `unicode`.

Additionally, type hinting appears to be still changing, and Python 3's type hinting style is not recognized in Python 2, which uses what is called "type comments". Take for instance Python 3.10.0 which introduced some changes with [PEP 604](https://www.python.org/dev/peps/pep-0604/).

So providing the same feature for both Python 2 and 3 was immediately out of the question, which made me take a simpler approach: restrict to Python's last 2.7 release, `2.7.18`.

Once that call was made I mainly relied on [PEP 484](https://www.python.org/dev/peps/pep-0484/), especially the section on [Python 2.7](https://www.python.org/dev/peps/pep-0484/#suggested-syntax-for-python-2-7-and-straddling-code), and `mypy`'s ["Type hints cheat sheet (Python 2)"](https://mypy.readthedocs.io/en/stable/cheat_sheet.html).

So to all of you who installed `ignition-api` using Python 3+, my most sincere apologies. But if at some point I see myself cornered and have no other option but to allow Python 3, I'll let all of you know.

Thanks for reading.

## Additional reading

* Python Types Intro - FastAPI - <https://fastapi.tiangolo.com/python-types/>
* Type checking Python 2 code - Mypy documentation - <https://mypy.readthedocs.io/en/stable/python2.html>
* Python Type Checking (Guide) - Real Python - <https://realpython.com/python-type-checking/#type-comments>
