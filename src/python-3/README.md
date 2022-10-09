Python Basics: My First Project
===============================

_Author(s): timfi (WS22/23)_

## Motivation

![](https://imgs.xkcd.com/comics/python_environment.png)

## How do I manage my Python version?

There are many situations where you need multiple versions of python installed on your machine. For example, when working at a company, they keep their projects off the bleeding edge and continue using older versions. So how do you install multiple versions of python? And how do you make them all available through the `python` command without getting them mixed up and tangled?

> _tl;dr_ use tools like `pyenv` or install python through “well behaved” package managers...

### Installing `pyenv`

_UNIX (& WSL)_ [^pyenv]
```bash
curl https://pyenv.run | bash
```

_Windows_ [^pyenv-win]
```powershell
Invoke-WebRequest -UseBasicParsing -Uri "https://raw.githubusercontent.com/pyenv-win/pyenv-win/master/pyenv-win/install-pyenv-win.ps1" -OutFile "./install-pyenv-win.ps1"; &"./install-pyenv-win.ps1"
```

### List available python versions

```bash
pyenv install --list
```

This will list all version of python that you can install via `pyenv`. Notice that there is a lot more available than just vanilla python, you can see things like `anaconda` or `pypy`!

### List installed python versions

```bash
pyenv versions
```

This will list any and all installed versions of python that you can access through `pyenv`.

### Installing a specific python version

```bash
pyenv install "3.10.6"
```

This installs the given version of python.

### Switch python version

_Switch to a specific version_
```bash
pyenv shell "3.10.6"
```

This sets the version of python your local shell instance has access to. This change will not be reflected in a different terminal.

_Switch to the system installation_
```bash
pyenv shell "system"
```

When setting the python version using `pyenv` you can always use the `system` version specifier, which represents the python interpreter installed by your operating system vendor.

### Set global python version

```bash
pyenv global "3.10.6"
```

This will set the version of python you get access to when you open a new terminal. Think of it as setting the default python version for your system.

### Set python version for a given directory

```bash
pyenv local "3.10.6"
```

This will create a file called `.python-version` in the folder! The presence of this file will cause `pyenv` to switch to and use the python version declared therein automatically.

## How do I manage my projects' dependencies?

Though python comes with many batteries included, one of the arguably most important ones for a modern programming language is missing — a proper way to install _project dependencies_. Though the standard library does contain `venv`[^venv] and `pip`[^pip] these lack support for modern standards like `pyproject.toml`[^pyprojecttoml].

> _tl:dr_ use tools like `poetry`[^poetry] or `pipenv`[^pipenv]

### Installing `poetry` [^poetry-install]

_UNIX (& WSL)_

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

_Windows_

```powershell
(Invoke-WebRequest -Uri https://install.python-poetry.org -UseBasicParsing).Content | py -
```

### Start a new project

To start a new project run the following command:

```bash
poetry new my_project
```

This will create a new directory named `my_project` with the proper layout:

```none
my_project
├── pyproject.toml
├── README.md
├── my_project
│   └── __init__.py
└── tests
    └── __init__.py
```

The `pyproject.toml` file will only be sparsely populated with information, as such it is good practice to fill this is as soon as possible! Some of the fields you should fill are `authors` and `description` — the former needs your name and email, the latter needs a short 5-15 word long description of your project.

```toml
[tool.poetry]
name = "my-project"
version = "0.1.0"
description = ""
authors = ["Your Name <you@example.com>"]
readme = "README.md"
packages = [{include = "my_project"}]

[tool.poetry.dependencies]
python = "^3.10"


[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

### Add project dependencies

Once you have a `poetry` project setup you can use the following command to add new dependencies to it.

```bash
poetry add "rich"
```

This adds the `rich` library and all of its dependencies as project dependencies. If you want to only add this library for development purposes — like it is common for unit test libraries or type checkers — you can add those dependencies to the `dev` dependency group.

```bash
poetry add "ward" --group "dev"
```

Sometimes dependencies require different versions of other packages or leave some ambiguity in which version is required. `poetry` resolves these ambiguities for you and makes sure to install the newest possible version of any dependecy by default.

### Types of dependencies

Usually you will install packages from what is called the Python Package Index [^pypi] — short PyPI. But there are situations where the newest versions of packages are not available through the index. These situations range from the packages in question being closed source and private all the way to the newest feature set being in alpha. If you desire to install such packages you will need to do so slightly differently.

```bash
poetry add "git+https://github.com/Textualize/textual.git#css"
```

This will install the `textual` package directly from the `css` branch of the GitHub repository.

Other libraries/packages will have additional feature sets that require additional dependencies. You can either install these manually or make use of their predefined “extra”-dependencies.

```bash
poetry add "typer[all]"
```

## How do I keep big projects readable?

When working collaborative projects — and for this assume that all open source projects are included — it is good practice to ensure a uniform code style. A uniform code style allows developers and users reading the code to learn the code style once and then not having to reason about code layout again. There are multiple ways to enforce code styles the most common are style linters and autoformatters. The latter of which makes adherence to a uniform code style as easy as saving a file.

> _tl;dr_ use autoformatters like `black`[^black]

### Adding `black` to your project

As discussed before there are many ways to install Python packages. But since we are setting up a reproducible project setup let's continue using `poetry`. As `black` is a development tool rather than an actual functional dependency of the project it should be installed as such, aka. via the `dev` group!

```bash
poetry add "black" --group "dev"
```

### Enable autoformatting with `black` in you editor/IDE

`black` is widely supported by editors and IDEs as a formatting provider[^black-integration]. For example, to enable `black`-based formatting in VS Code you only need to flip a few switches in the settings:

1. Enable the `editor.formatOnSave` setting
2. Set the `python.formatting.provider` setting to `black`

## How do I keep my sanity in complicated code?

Another important tool for large code bases is a type checker. This may seem counterintuitive, as Python is often heralded as the language with the best dynamic type system, but type safety and correctness is imperative for well functioning code. Additional type information can be added statically throughout Python code via _type annotations_ since Python 3.6. Since then static type checkers that ensure the soundness of both the type annotations and the code using them have become an invaluable tool.

> _tl;dr_ use static type checkers like `mypy`[^mypy] or `pyright`[^pyright]

### Installing `pyright`

To keep things simple we will opt to use `pyright` for this workshop as it is already integrated into the `Python`[^vscode-python] extension for VS Code. Tough it can also be used with other editors[^pyright-integration]. If you are using PyCharm it also already contains a proprietary type checker. 😉

### Using type annotations

Take, for example, the following function:

```python
def my_sum(numbers):
    result = 0
    for number in numbers:
        result += number
    return result
```

If you call it via `my_sum([1,2,3,4])` its no problem. But what happens if you do the following `my_sum(1,2,3)`? You get a `TypeError: 'NoneType' object is not iterable` at runtime! These kinds of errors happen regularly in complicated and/or large code bases — simply because you loose sight of the big picture. In such situations its nice to be notified by you linter/editor that this could can/will go awry at runtime. To be able to give such advice we need to fill in some information that the type checker can guess — or rather infer — on its own.

```python
def my_sum(numbers: list[float]) -> float:
    result = 0
    for number in numbers:
        result += number
    return result
```

These type annotations and checkers thereof also enable standard modules like `dataclasses`[^dataclasses] to show their full potential. The following the `class` definitions are effectively™ identical™ but the one using the `@dataclass` decorator is actually type safe!

```python
class BoringVec2:
    def __init__(self, x, y, z):
        self.x = x
        self.y = y
        self.z = z

from dataclasses import dataclass

@dataclass
class Vec2:
    x: float
    y: float
    z: float
```

To express more complicated types in a succinct manner Python's standard library has the `typing` module[^typing]. With the typing module we can also properly annotate polymorphic functions!

```python
from typing import TypeVar

T = TypeVar('T', int, float)

def my_sum(numbers: list[T]) -> T:
    result = 0
    for number in numbers:
        result += number
    return result
```

When using older Python versions you can backport some of the advancements of the `typing` module by installing the `typing_extension` package[^typing_extension]. This package also usually contains proxies for newer typing features that are still in beta and type checkers also usually support these.

## How do I publish my code?

<!-- ![TODO] package building via `poetry build` -->
<!-- ![TODO] package publishing via `poetry publish` -->
<!-- ![TODO] package building + publishing via `poetry publish --build` -->
<!-- ![TODO] skip existing versions via `poetry publish --skip-existing` -->

<!-- Footnotes: -->
[^pyenv]: https://github.com/pyenv/pyenv
[^pyenv-win]: https://github.com/pyenv-win/pyenv-win
[^venv]: https://docs.python.org/3/library/venv.html
[^pip]: https://pip.pypa.io/en/latest/
[^pyprojecttoml]: https://pip.pypa.io/en/stable/reference/build-system/pyproject-toml/
[^pipenv]: https://pipenv.pypa.io/en/latest/
[^poetry]: https://python-poetry.org/
[^poetry-install]: https://python-poetry.org/docs/#installing-with-the-official-installer
[^pypi]: https://pypi.org/
[^black]: https://github.com/psf/black
[^black-integration]: https://black.readthedocs.io/en/stable/integrations/editors.html
[^mypy]: http://mypy-lang.org/
[^pyright]: https://github.com/microsoft/pyright
[^vscode-python]: https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance
[^pyright-integration]: https://github.com/microsoft/pyright#installation
[^dataclasses]: https://docs.python.org/3/library/dataclasses.html
[^typing]: https://docs.python.org/3/library/typing.html
[^typing_extension]: https://github.com/python/typing_extensions