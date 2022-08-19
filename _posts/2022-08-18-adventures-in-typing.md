---
layout: post
title: Adventures in typing
subtitle: The "obscure" AttributeError exception
tags:
- ignition-api
- incendium
- jython
- mypy
- pip
- pypi
- python
- typing
date: 2022-08-18 16:43 -0700
---
Python packaging series:

* [Lessons learned in packaging Python projects]({{ site.url }}/2021/10/26/lessons-learned-in-packaging-python-projects/)
* [ignition-api package and typing]({{ site.url }}/2021/12/10/ignition-api-package-and-typing/)

Welcome to the third installment of what now I call the "Python packaging series".

Last time I wrote about my progress and lessons learned while creating the `ignition-api` Python package, and a lot has happened in the last eight months, so let me fill you in.

* In March 2022, the **ignition-api** [GitHub organization](https://github.com/ignition-api) was born
  * With that, each branch of the **thecesrom/Ignition** repo was moved into its own repo; [jython](https://github.com/ignition-api/jython), [7.9](https://github.com/ignition-api/7.9), [8.0](https://github.com/ignition-api/8.0), and [8.1](https://github.com/ignition-api/8.1)
* In June 29 2022, `ignition-api-stubs` got its Alpha release, and a month later it got its Beta release
  * Some valuable knowledge in typing was gained (more on `stubgen` and `TypedDict` in another post)
* `git-cliff` was replaced by `commitizen` on actively developed repos

That pretty much sums up the last couple of months.

And now, onto our main topic.

As I continued working on improving and adding type hints (or comments) in baby steps to the `ignition-api` package, and then decided to accept Unicode literals and not just `str`, I added the following import to all modules:

```python
from __future__ import unicode_literals
```

The reason why I decided to create an alias called `String` instead of using `AnyStr` from `typing` escapes me at this moment, but a quick test shows that `mypy` throws an error in a function where I specifically return `unicode`; so maybe that was the "why".

```python
String = Union[str, unicode]
```

After some debate, I decided to refactor my code and move the `String` alias to `java.lang`, where it belonged all along. Enter commit [ea530ac](https://github.com/thecesrom/Ignition/commit/ea530ac).

`ignition-api` [8.1.13](https://pypi.org/project/ignition-api/8.1.13/) was released in December 2021, and I did not realize my mistake until January 2022 when I upgraded my virtual environment for my `incendium` project and tried to run `pip list`, only then it dawned upon me I had pushed a bad release.

Once anyone updated to `ignition-api` 8.1.13, `pip` under their Python environment (or installation) ceased to work, and it threw the following error:

```sh
Traceback (most recent call last):
  File "/Users/thecesrom/.pyenv/versions/2.7.18/lib/python2.7/runpy.py", line 174, in _run_module_as_main
    "__main__", fname, loader, pkg_name)
  File "/Users/thecesrom/.pyenv/versions/2.7.18/lib/python2.7/runpy.py", line 72, in _run_code
    exec code in run_globals
  File "/Users/thecesrom/.pyenv/versions/2.7.18/envs/incendium/lib/python2.7/site-packages/pip/__main__.py", line 23, in <module>
    from pip._internal.cli.main import main as _main  # isort:skip # noqa
  File "/Users/thecesrom/.pyenv/versions/incendium/lib/python2.7/site-packages/pip/_internal/cli/main.py", line 10, in <module>
    from pip._internal.cli.autocompletion import autocomplete
  File "/Users/thecesrom/.pyenv/versions/incendium/lib/python2.7/site-packages/pip/_internal/cli/autocompletion.py", line 4, in <module>
    import optparse
  File "/Users/thecesrom/.pyenv/versions/2.7.18/lib/python2.7/optparse.py", line 90, in <module>
    from gettext import gettext
  File "/Users/thecesrom/.pyenv/versions/2.7.18/lib/python2.7/gettext.py", line 49, in <module>
    import locale, copy, os, re, struct, sys
  File "/Users/thecesrom/.pyenv/versions/2.7.18/lib/python2.7/copy.py", line 60, in <module>
    from org.python.core import PyStringMap
  File "/Users/thecesrom/.pyenv/versions/incendium/lib/python2.7/site-packages/org/python/core/__init__.py", line 5, in <module>
    from java.lang import Object
  File "/Users/thecesrom/.pyenv/versions/incendium/lib/python2.7/site-packages/java/lang/__init__.py", line 22, in <module>
    from typing import Union
  File "/Users/thecesrom/.pyenv/versions/incendium/lib/python2.7/site-packages/typing.py", line 1440, in <module>
    copy._copy_dispatch[GenericMeta] = _copy_generic
AttributeError: 'module' object has no attribute '_copy_dispatch'
```

I immediately panicked, and reverted the `java.lang.String` change, and released `ignition-api` 8.1.13.post1.

Searching the Internet gave me no answers. Apparently no one had seen or experienced something like this.

Admitting defeat, I left things as they were for a while, but after a while I went back and followed the breadcrumbs - or should I say traceback?

`typing.py:1440`:

```python
copy._copy_dispatch[GenericMeta] = _copy_generic
```

That was not it.

`java/lang/__init__.py:22`:

```python
String = Union[str, unicode]
```

That was not meaningful.

`org/python/core/__init__.py:5`:

```python
from java.lang import Object
```

What??? Onto the next one.

`copy.py:60`:

```python
try:
    from org.python.core import PyStringMap  # this is line 60
except ImportError:
    PyStringMap = None
```

But why?

If my mocking of `org.python.core` within `ignition-api` was to blame, wasn't this supposed to be caught as the `ImportError`?

The answer is: No.

So I tried adding the following line, and same thing.

```python
PyStringMap = None
```

The solution came to me by accident, because while I was editing on Visual Studio Code it automatically added the `import` statement which solved my issue.

```python
from copy import PyStringMap
```

Something I would have never thought of doing, but it fixed my issue. Issue [`ignition-api/8.1#28`](https://github.com/ignition-api/8.1/issues/28) was created and immediately fixed by PR [`ignition-api/8.1#29`](https://github.com/ignition-api/8.1/pull/29).

```sh
$ git diff e4d494e 3beb4d5 src/org/python/core/__init__.py
diff --git a/src/org/python/core/__init__.py b/src/org/python/core/__init__.py
index 0f4e826..4f36856 100644
--- a/src/org/python/core/__init__.py
+++ b/src/org/python/core/__init__.py
@@ -1,8 +1,15 @@
 from __future__ import print_function

-__all__ = ["PyObject", "PyType"]
+from copy import PyStringMap

-from java.lang import Object
+__all__ = ["PyObject", "PyStringMap", "PyType"]
```

And with that, my friends, the `AttributeError: 'module' object has no attribute '_copy_dispatch'` bug was squashed.

Thanks for reading!

P.S. `copy.py` lines 59-63 were committed by no other than [@gvanrossum](https://github.com/gvanrossum) back in November 27, 2000 in [`f8baad0`](https://github.com/python/cpython/commit/f8baad0f1759a5b26b050636fd327e874e17c5a0#diff-a06198e13686594114a45e7863b954a226ce08d43f724079faa7fe8b3c931703R59-R63) which took 21 years to come bite me (or save me?)
