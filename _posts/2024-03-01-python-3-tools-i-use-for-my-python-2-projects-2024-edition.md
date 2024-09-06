---
layout: post
title: Python 3 tools I use for my Python 2 projects - 2024 edition
subtitle: Fighting to keep Python 2 alive
tags:
- coding standards
- java
- jython
- python
date: 2024-03-01 12:56 -0800
last-updated: 2024-09-05 17:00 -0700
---
## TOC

- [Introduction](#introduction)
- [Background](#background)
- [The Python 3 toolset](#the-python-3-toolset)
- [Which Python 3?](#which-python-3)
- [Packaging and publishing](#packaging-and-publishing)
- [CI/CD](#cicd)
- [Conclusion](#conclusion)

## Introduction

Welcome to part 5 on coding standards and code quality:

- [79 characters]({{ site.url }}/2020/12/22/79-characters)
- [How I use black, flake8 and isort to format Python2 code]({{ site.url }}/2021/03/06/how-i-use-black-flake8-and-isort-to-format-python2-code)
- [pre-commit.ci and pydocstyle join the family]({{ site.url }}/2021/06/21/pre-commit-ci-and-pydocstyle-join-the-family/)
- [Of camels and snakes]({{ site.url }}/2021/06/26/of-camels-and-snakes/)

While I still use the same tools I listed [here]({{ site.url }}/2021/03/06/how-i-use-black-flake8-and-isort-to-format-python2-code), I will go over tools I am currently using.

## Background

I still actively work on multiple Python 2 packages because they're targeted at Ignition Developers, and Ignition relies on Jython 2.7 for scripting, and while some may find this a bit restricting, this has forced me to be more creative when I am not able to find a pure Python 2 module/library I can always look into Java 11 to assist. For example, I provided some feedback on a solution for monitoring remaining drive space where `java.io.File` and `java.net.InetAddress` have become very useful.

>Eventhough we don't use the most recent version of Python, I believe that it is within these restrictions (especially because we're using Jython) that we have the opportunity to demonstrate that even being limited by it, we are capable of producing applications of the highest level.
>
>Anyone out there can just simply install `pandas` and `numpy`, but we have the opportunity to explore more options and even come up with our own.

I've seen Python 3 projects where they use the `requests` library only for `POST`, while I was able to explore `urllib2` for doing exactly that.

## The Python 3 toolset

My tools can be grouped into the following categories:

- Formatters
  - `black`
  - `docformatter`
  - `isort`
  - `sort-all`
  - `ssort`
- Linters
  - `flake8==5.0.4`
  - `pydocstyle`
  - `pylint`
  - `sourcery-cli`
- Type checkers
  - `mypy[python2]==0.971`
- Automation
  - `tox`
    - With the following recommendation from tox.wiki on [testing end-of-life Python versions](https://tox.wiki/en/latest/faq.html#testing-end-of-life-python-versions)
  - `nox`
    - I tried it, but I couldn't make it work with private repositories in Azure DevOps. See: [wntrblm/nox#709](https://github.com/wntrblm/nox/issues/709).
- Testing
  - `pytest`
- Packaging and publishing
  - `build`
  - `twine`
- Package management
  - `pip-tools`
- Others
  - `check-jsonschema`
  - `commitizen`
  - `pipx`
  - `pre-commit`

## Which Python 3?

As of today, Python has five active releases: 3.8, 3.9, and 3.10 in the "security fixes only" where no binary installers are provided, 3.11, and 3.12.

While in the past I have written about [typing]({{ site.url }}/tags/#typing), I have not clarified which Python 3 version I am using to install and run `mypy`; so let's clear that out.

I had been using Python 3.10 for a while, and when 3.11 was released I ran into issues when I tried installing `mypy[python2]==0.971`, which I vaguely remember were related with `typed_ast`. So for the longest time I didn't go back and test 3.11, and I even started creating a Python 3.10 [installer](https://github.com/coatl-dev/cpython/releases) starting with 3.10.12, as no binary installer was provided.

The same thing happened when Python 3.12 was released, but I have recently tested 3.12.2 and I am happy to say that my CI is running 3.12.

~~As of the time of the posting of this article, `mypy[python2]==0.971` fails to install on 3.13.0a4.~~

~~Update (2024-03-01): I just tried installing `mypy[python2]==0.971` using Python 3.13.0a4 and it worked. Securing my GitHub workflows and Azure Pipelines till October 2029.~~

Update (2024-09-05): Unfortunately Python 3.13 does not allow `mypy[python2]==0.971` to be installed.

Trying to install it results in the following error:

```sh
$ python -m pip install 'mypy[python2]==0.971'
Collecting mypy==0.971 (from mypy[python2]==0.971)
  Downloading mypy-0.971-py3-none-any.whl.metadata (1.8 kB)
Collecting typing-extensions>=3.10 (from mypy==0.971->mypy[python2]==0.971)
  Downloading typing_extensions-4.12.2-py3-none-any.whl.metadata (3.0 kB)
Collecting mypy-extensions>=0.4.3 (from mypy==0.971->mypy[python2]==0.971)
  Downloading mypy_extensions-1.0.0-py3-none-any.whl.metadata (1.1 kB)
Collecting typed-ast<2,>=1.4.0 (from mypy[python2]==0.971)
  Downloading typed_ast-1.5.5.tar.gz (252 kB)
  Preparing metadata (setup.py) ... done
Downloading mypy-0.971-py3-none-any.whl (2.6 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 2.6/2.6 MB 17.1 MB/s eta 0:00:00
Downloading mypy_extensions-1.0.0-py3-none-any.whl (4.7 kB)
Downloading typing_extensions-4.12.2-py3-none-any.whl (37 kB)
Building wheels for collected packages: typed-ast
  Building wheel for typed-ast (setup.py) ... error
  error: subprocess-exited-with-error

  × python setup.py bdist_wheel did not run successfully.
  │ exit code: 1
  ╰─> [30 lines of output]
      running bdist_wheel
      running build
      running build_py
      creating build
      creating build/lib.linux-x86_64-cpython-313
      creating build/lib.linux-x86_64-cpython-313/typed_ast
      copying typed_ast/ast27.py -> build/lib.linux-x86_64-cpython-313/typed_ast
      copying typed_ast/conversions.py -> build/lib.linux-x86_64-cpython-313/typed_ast
      copying typed_ast/ast3.py -> build/lib.linux-x86_64-cpython-313/typed_ast
      copying typed_ast/__init__.py -> build/lib.linux-x86_64-cpython-313/typed_ast
      creating build/lib.linux-x86_64-cpython-313/typed_ast/tests
      copying ast3/tests/test_basics.py -> build/lib.linux-x86_64-cpython-313/typed_ast/tests
      running build_ext
      building '_ast27' extension
      creating build/temp.linux-x86_64-cpython-313
      creating build/temp.linux-x86_64-cpython-313/ast27
      creating build/temp.linux-x86_64-cpython-313/ast27/Custom
      creating build/temp.linux-x86_64-cpython-313/ast27/Parser
      creating build/temp.linux-x86_64-cpython-313/ast27/Python
      gcc -fno-strict-overflow -Wsign-compare -DNDEBUG -g -O3 -Wall -fPIC -Iast27/Include -I/usr/local/include/python3.13 -c ast27/Custom/typed_ast.c -o build/temp.linux-x86_64-cpython-313/ast27/Custom/typed_ast.o
      In file included from /usr/local/include/python3.13/pyport.h:358,
                       from /usr/local/include/python3.13/Python.h:50,
                       from ast27/Custom/typed_ast.c:1:
      ast27/Custom/../Include/compile.h:12:12: error: unknown type name ‘PyFutureFeatures’
         12 | PyAPI_FUNC(PyFutureFeatures *) PyFuture_FromAST(struct _mod *, const char *);
            |            ^~~~~~~~~~~~~~~~
      /usr/local/include/python3.13/exports.h:94:53: note: in definition of macro ‘PyAPI_FUNC’
         94 | #       define PyAPI_FUNC(RTYPE) Py_EXPORTED_SYMBOL RTYPE
            |                                                     ^~~~~
      error: command '/usr/bin/gcc' failed with exit code 1
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
  ERROR: Failed building wheel for typed-ast
  Running setup.py clean for typed-ast
Failed to build typed-ast
ERROR: ERROR: Failed to build installable wheels for some pyproject.toml based projects (typed-ast)
```

This is because starting with version 3.13.0a6 `PyFutureFeatures` was moved to an internal header and made private. Breaking installation for all future releases.

This means 3.12 is the last version to allow installation of `mypy[python2]==0.971`, so we will be covered till October 2028.

## Packaging and publishing

On my previous [post]({{ site.url }}/2023/04/29/ci-cd-the-april-2023-python2-apocalypse/) I wrote about how I was expecting GitHub to keep Python 2.7.18 in their `ubuntu-20.04`'s tool cache until EOL, but they have decided to remove it on May 15, 2023.

In all of my projects I depend on reusable workflows I have created under the `coatl-dev` GitHub organization, which can be found [here](https://github.com/coatl-dev/workflows), and I am using the [`pypi-upload`](https://github.com/coatl-dev/workflows#githubworkflowspypi-uploadyml) reusable workflow for my Python 2 and 3 packages.

## CI/CD

Finally, CI/CD. As previously mentioned, I have created reusable [workflows](https://github.com/coatl-dev/workflows) and GitHub [actions](https://github.com/coatl-dev/actions), which I am using accross multiple project in both the [ignition-api](https://github.com/ignition-api) and [ignition-incendium](https://github.com/ignition-incendium) GitHub organizations.

But most importantly, I created a Docker image named [six](https://hub.docker.com/r/coatldev/six), based on Ubuntu 22.04 with Python 2.7 and 3 pre-installed, to overcome the Python 2 apocalypse.

## Conclusion

As you may have realized, necessity is the mother of invention. Feel free to use what I've created.

Thanks for reading.
