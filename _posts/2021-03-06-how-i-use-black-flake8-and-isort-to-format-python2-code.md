---
layout: post
title: How I use black, flake8 and isort to format Python2 code
subtitle: and how I put it all together with pre-commit
tags:
- coding standards
- homebrew
- pip
- python
date: 2021-03-06 18:35 -0800
last-updated: 2021-06-21 19:28 -0700
---
Welcome to part 2 of my series on how code formatters can assist in producing easy-to-read code, and hopefully easy to maintain too.

On this installment I will discuss how I use tools like [black](#black), [isort](#isort), [flake8](#flake8), and how to put it all together using [pre-commit](#pre-commit).

In, what is now, [part 1]({{ site.url }}/2020/12/22/79-characters) I  mentioned that due to some work-related projects I still write Python 2 code, but as everyone is dropping support for Python 2, I have decided to use Python 3 tools to format my code.

So let's begin by describing each tool, and how I use them.

### black

> The Uncompromising Code Formatter

With `black` you can format Python code from 2.7 all the way to 3.8 (as of version 20.8b1), which makes for a great replacement of [YAPF](https://github.com/google/yapf){:target="_blank"} which can only format code depending on the Python version being used to run it.

My preference is using [PEP 8](https://www.python.org/dev/peps/pep-0008/){:target="_blank"} as my style guide, and so, 79-characters per line of code is what I use. So it's as simple as running the following code at the root of my project and all non-compliant files will be reformatted:

```bash
black --line-length 79 --target-version py27 .
```

Let's explain each option.

- `-l` or `--line-length`: How many characters per line to allow. [default: 88]
- `-t` or `--target-version`: Python versions that should be supported by Black's output. [default: per-file auto-detection]

Fairly simple. Allow `79` characters per line, and use `py27` as the targetted version.

### isort

> A Python utility / library to sort imports.

And just as their slogan states: "isort your imports, so you don't have to."

Command:

```bash
isort --multi-line 3 --profile black --python-version 27 .
```

The options used are mainly to be compatible with `black` (see [here](https://pycqa.github.io/isort/docs/configuration/black_compatibility/){:target="_blank"}):

- `--multi-line`: Multi line output (0-grid, 1-vertical, 2-hanging, 3-vert-hanging, 4-vert-grid, 5-vert-grid-grouped, 6-vert-grid-grouped-no-comma, 7-noqa, 8-vertical-hanging-indent-bracket, 9-vertical-prefix-from-module-import, 10-hanging-indent-with-parentheses).
  - `3-vert-hanging`
- `--profile`: Base profile type to use for configuration. Profiles include: black, django, pycharm, google, open_stack, plone, attrs, hug. As well as any shared profiles.
  - `black`
- `--python-version`: Tells isort to set the known standard library based on the specified Python version. Default is to assume any Python 3 version could be the target, and use a union of all stdlib modules across versions. If auto is specified, the version of the interpreter used to run isort (currently: 39) will be used.
  - `27` for Python 2.7

But there's still something missing. `black` does not care about comments or [docstrings](https://www.python.org/dev/peps/pep-0257/){:target="_blank"}, and `isort` cares even less, for obvious reasons; enter `flake8`.

### flake8

> flake8 is a python tool that glues together pep8, pyflakes, mccabe, and third-party plugins to check the style and quality of some python code.

Anthony Sottile ([@asottile](https://gitlab.com/asottile){:target="_blank"}) has mentioned that he plans to drop support for Python 2.7 in future releases, maybe in version [3.9 or 4.0](https://gitlab.com/pycqa/flake8/-/issues/690){:target="_blank"}.

Fortunately I can still use it for Python 2 by running the following command:

```bash
flake8 --max-doc-length=72 --ignore=E211,E999,F401,F821,W503
```

[PEP 8](https://www.python.org/dev/peps/pep-0008/){:target="_blank"} recommends limiting docstrings or comments to 72 characters, which is exactly what I'm using for flake8.

So let's explain each option used.

- `--max-doc-length`: Maximum allowed doc line length for the entirety of this run. (Default: None)
- `--ignore`: Comma-separated list of errors and warnings to ignore (or skip). For example, ``--ignore=E4,E51,W234``. (Default: ['E226', 'E123', 'W504', 'E121', 'W503', 'E126', 'E704', 'E24'])

In my case I am using `72` as the maximum allowed characters for my docstrings, in accordance to PEP 8, and ignoring the following errors:

- `E211`: whitespace before ‘(‘
  - Since in Python 2 `print` is not a function, `black` adds a space between the `print` statement from Python 2, and the openning parenthesis
- `E999`: SyntaxError: invalid syntax
  - In my case this occurs again with the `print` statement where I am printing just one arguments like this `print arg`
- `F401`: `module` imported but unused
  - I do import some modules in order to get "Intellisense" when I peek into the details in PyCharm
- `F821`: undefined name `name`
  - In one of my libraries I am checking if an argument is a string, and in order to cover my bases with plain strings (`str`) and unicode, I found that using `basestring` would work for all characters, including non-Latin characters
- `W503`: line break before binary operator
  - It doesn't like when binary operators are broken into multi-line statements

### pre-commit

> A framework for managing and maintaining multi-language pre-commit hooks.

Finally, let's put it all together with [pre-commit](https://pre-commit.com/){:target="_blank"}.

So in order to use `flake8` you'll have to create a `.flake8` file. Mine looks like this:

```properties
[flake8]
ignore = E211, E999, F401, F821, W503
max-doc-length = 72
```

A `pyproject.toml` file that in my case looks like this:

```properties
[tool.black]
line-length = 79
target-version = ['py27']

[tool.isort]
profile = "black"
multi_line_output = 3
py_version = 27
```

And finally my `.pre-commit-config.yaml` file:

```yml
repos:
  - repo: https://github.com/psf/black
    rev: 20.8b1
    hooks:
      - id: black
  - repo: https://github.com/PyCQA/isort
    rev: 5.7.0
    hooks:
      - id: isort
  - repo: https://github.com/PyCQA/flake8
    rev: 3.8.4
    hooks:
      - id: flake8
```

After you've configured all of this for the first time, first run the `install` command for `pre-commit` and to run tests I use `run` with the `--all-files` option, just like this:

```bash
$ pre-commit install
pre-commit installed at .git/hooks/pre-commit
$ pre-commit run --all-files
black....................................................................Passed
isort....................................................................Passed
flake8...................................................................Passed
```

So every time you try to commit something to your Git repo, all tests should be marked as `Passed`, otherwise the commit will fail.

## Bonus

At the moment of writing this post both `black` and `isort` do support the use of `pyproject.toml`, something that `flake8` still hasn't implemented unlike [`flake9`](https://pypi.org/project/flake9/){:target="_blank"} or [FlakeHell](https://flakehell.readthedocs.io/){:target="_blank"}, which I have not integrated into my workflow; I'm still using `flake8` because I've installed it via Homebrew.

While you have the option to "`pip`-install" all of these tools, currently I decided to use Homebrew because I don't usually check if my packages are outdated, something that Homebrew contributors actually do with each new release. See: [`black`](https://formulae.brew.sh/formula/black){:target="_blank"}, [`flake8`](https://formulae.brew.sh/formula/flake8){:target="_blank"}, and [`isort`](https://formulae.brew.sh/formula/flake8){:target="_blank"}, which will install [`python@3.9`](https://formulae.brew.sh/formula/python@3.9){:target="_blank"} as they all depend on it.

But if you do use `pip`, I recommend adding an alias for updating all of your outdated packages that should run the following command:

```bash
python -m pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1 | xargs -n1 python -m pip install --upgrade
```

## Other useful `pip` packages

- [`pip-autoremove`](https://pypi.org/project/pip-autoremove/){:target="_blank"} for removing packages and all of their dependencies.
- [`pylint`](http://pylint.pycqa.org/en/latest/){:target="_blank"}, a static code analysis tool which looks for programming errors, helps enforcing a coding standard, sniffs for code smells and offers simple refactoring suggestions.

Happy coding!
