---
layout: post
title: pre-commit.ci and pydocstyle join the family
tags:
- coding standards
- pip
- pre-commit
- python
date: 2021-06-21 13:38 -0700
---
Welcome to part 3 on coding standards and code quality; these are [part 1]({{ site.url }}/2020/12/22/79-characters), and [part 2]({{ site.url }}/2021/03/06/how-i-use-black-flake8-and-isort-to-format-python2-code).

This time we will discuss [`pre-commit.ci`](https://pre-commit.ci){:target="_blank"} and [`pydocstyle`](https://www.pydocstyle.org/en/stable/){:target="_blank"}, the latest additions to my toolbox.

First, `pre-commit.ci`. `pre-commit.ci` is ***"a continuous integration service for the [pre-commit](https://pre-commit.com/){:target="_blank"} framework"*** created by Anthony Sottile ([GitHub](https://github.com/asottile){:target="_blank"}, [Twitter](https://twitter.com/codewithanthony){:target="_blank"}), which is described as follows:

> Developers spend a fair chunk of time during their development flow on fixing relatively trivial problems in their code. pre-commit.ci both enforces that these issues are discovered (which is opt-in for each developer's workflow via pre-commit) but also fixes the issues automatically, letting developers focus their time on more valuable problems.

I have enabled `pre-commit.ci` on two of my repos ([Ignition](https://github.com/thecesrom/Ignition){:target="_blank"} and [incendium](https://github.com/thecesrom/incendium){:target="_blank"}) and so far three PRs have been created and successfully merged.

In order to install `pre-commit.ci`, I signed in using my GitHub account and granted access to both repos, and that was it! I had already created the `.pre-commit-config.yaml` file for `pre-commit`, and although it is not required I did add a `ci` section to that file and customize the `autoupdate_commit_msg` value to match the [Conventional Commits](https://www.conventionalcommits.org/){:target="_blank"} style I adopted back in February. Learn more about `pre-commit.ci` configuration [here](https://pre-commit.ci/#configuration){:target="_blank"}.

This is how my `.pre-commit-config.yaml` file looks:

```yml
ci:
  autoupdate_commit_msg: 'build(pre-commit.ci): pre-commit autoupdate'

repos:
  - repo: https://github.com/psf/black
    rev: 21.6b0
    hooks:
      - id: black
  - repo: https://github.com/PyCQA/isort
    rev: 5.9.1
    hooks:
      - id: isort
  - repo: https://github.com/PyCQA/flake8
    rev: 3.9.2
    hooks:
      - id: flake8
  - repo: https://github.com/PyCQA/pydocstyle
    rev: 6.1.1
    hooks:
      - id: pydocstyle
        additional_dependencies:
          - toml
```

Next, `pydocstyle`. `pydocstyle` ***"is a static analysis tool for checking compliance with Python docstring conventions."*** It can be installed via `python -m pip install pydocstyle` or alternatively you could use `pydocstyle.py` source file directly as it is self-contained.

Right after `pip-installing` it, I did run into a few issues, mainly because of the `print` statement from Python 2.7 which were resolved by importing `print_function` from `__future__`.

Also, I was lucky to run into `pydocstyle` right after they [enabled full `toml` configuration and `pyproject.toml`](https://github.com/PyCQA/pydocstyle/commit/8d8b319e6423d2e55fa1b0c9d456b4cf5d66d552){:target="_blank"}, since I was already using `pyproject.toml` for `black` and `isort`. In my case I had to add the following section to my `pyproject.toml` file on the Ignition project:

```toml
[tool.pydocstyle]
convention = "google"
add_ignore = ["D205", "D415"]
```

And since I am copying the docstrings provided by Inductive Automation and they don't subject themselves to [`PEP 257`](http://www.python.org/dev/peps/pep-0257/){:target="_blank"}, I did have to ignore the following error codes:

- `D205`: 1 blank line required between summary line and description
- `D415`: First line should end with a period, question mark, or exclamation point

Both apply to the summary line expanding over more than one line.

Also notice that on the `hooks` for `pydocstyle` on my `.pre-commit-config.yaml` I had to add `toml` as an `additional_dependency`, otherwise you might run into issues when running `pre-commit` hooks.

So now I use `black`, `isort`, `flake8`, and `pydocstyle`, in that order as recommended by Anthony Sottile himself [here](https://www.pythonpodcast.com/flake8-static-analysis-episode-309/){:target="_blank"}, all enforced using `pre-commit`.

Lastly, if you can, please consider supporting Anthony Sottile via [GitHub Sponsors](https://github.com/sponsors/asottile) to show your appreciation.

Thanks for reading!
