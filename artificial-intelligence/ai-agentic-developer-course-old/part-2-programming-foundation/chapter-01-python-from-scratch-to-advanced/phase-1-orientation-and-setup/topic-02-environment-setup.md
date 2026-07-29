# Topic 02: Environment Setup

Every programming journey has a first hurdle that has nothing to do with programming: getting the language onto your machine in a way you will not regret three months later. Python's setup story has historically been the worst part of learning it. Beginners were told to download an installer from python.org, then discovered that their operating system already had a different Python, that tutorials assumed a third one, and that installing packages for one project broke another. The mess even has a famous comic: [xkcd 1987, "Python Environment"](https://xkcd.com/1987/), a flowchart of arrows pointing everywhere.

This topic skips the mess entirely. You will install one modern tool, and that tool will handle everything: fetching Python versions, switching between them, creating isolated project environments, and installing packages. By the end you will have run your first Python code, and more importantly, you will understand *what* you installed and why each piece exists.

## The Moving Parts

Before typing any commands, meet the four things a working Python setup consists of. Concepts first, tooling second.

**The interpreter.** Python is an interpreted language: your code is not compiled to a standalone binary the way Rust or C code is. Instead, a program called the Python interpreter reads your `.py` files and executes them. "Installing Python" means installing this interpreter. It has versions (3.12, 3.13, 3.14), and as Topic 01: Python: History, Versions, and Governance established, different projects may need different ones, so a good setup can hold several side by side.

**Packages.** Almost nothing interesting is written from scratch. Python code leans on **packages**: reusable libraries published by the community to a central index called [PyPI](https://pypi.org/) (the Python Package Index), which hosts over half a million of them. NumPy, PyTorch, and everything else this curriculum will use arrives as a package. A package manager downloads and installs them.

**Virtual environments.** Here is the trap that ruined a generation of setups. If every project installs its packages into one shared, global Python, projects collide: project A needs version 1 of a library, project B needs version 2, and whichever installed last wins. The fix is the **virtual environment**: a lightweight, self-contained folder holding a project's own interpreter link and its own packages, isolated from every other project. One project, one environment, no collisions. This is not an optional nicety; it is how all professional Python is done.

**An editor.** Any text editor can write Python, but a modern editor adds autocomplete, inline error checking, and a debugger. This topic sets one up at the end.

The traditional stack used a separate tool for each part: an installer or `pyenv` for interpreters, `pip` for packages, `venv` for environments. They work, but wiring them together is exactly the xkcd flowchart. The modern answer collapses all of it into one tool.

## One Tool: uv

[uv](https://docs.astral.sh/uv/) is a Python toolchain manager built by Astral, and it has rapidly become the community standard. It manages Python versions, virtual environments, and packages in a single binary, and it is dramatically faster than the tools it replaces: package installs that took `pip` a minute take uv seconds. It is also, satisfyingly for anyone arriving from systems programming, written in Rust.

Install it with one command.

On macOS or Linux:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

On Windows (PowerShell):

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Then open a *new* terminal (so your shell picks up the new command) and verify:

```bash
uv --version
```

If a version number prints, uv is installed. Note what you did *not* do: you did not install Python yet. uv will do that for you, on demand, in the next section. The [uv installation docs](https://docs.astral.sh/uv/getting-started/installation/) cover alternative install methods if the script route is blocked on your machine.

## Installing Python and First Contact

Ask uv to fetch the current stable Python from Topic 01: Python: History, Versions, and Governance:

```bash
uv python install 3.14
```

Seconds later you have an interpreter. Meet it in its most direct form, the **REPL** (read-eval-print loop), an interactive session where you type Python and it answers immediately:

```bash
uv run python
```

You should see a prompt like this:

```text
Python 3.14.x (main, ...) 
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

The `>>>` is Python waiting for you. Try a few lines:

```python
>>> 2 + 2
4
>>> print("Hello, SOCC")
Hello, SOCC
>>> import this
```

That last one prints the Zen of Python from Topic 01: Python: History, Versions, and Governance, now running on your own machine. Exit the REPL with `exit()` or Ctrl-D (Ctrl-Z then Enter on Windows).

The REPL is for experiments; real programs live in files. Create a file named `hello.py` containing:

```python
print("Hello from a file")
```

and run it:

```bash
uv run python hello.py
```

REPL for poking at ideas, files for programs you keep: you will use both constantly, and Topic 03: Program Structure and Execution examines what actually happens when that file runs.

## Projects and Virtual Environments

Now the professional habit. Every project gets its own folder and its own environment, and uv makes the whole ritual three commands. Create a practice project:

```bash
uv init socc-playground
cd socc-playground
```

Look at what appeared. Among a few starter files, the important one is `pyproject.toml`: the standard manifest file describing a Python project, its required Python version, and its dependencies. If you have seen `Cargo.toml` in Rust, this is Python's equivalent, standardized across the ecosystem.

Add a package to the project:

```bash
uv add requests
```

Two things happened. First, uv created a virtual environment in a hidden `.venv` folder inside the project and installed `requests` (a popular HTTP library, properly introduced in Topic 18: Consuming APIs) into it, touching nothing outside this folder. Second, it recorded the dependency in `pyproject.toml` and pinned exact versions in a lockfile called `uv.lock`, so the same environment can be rebuilt identically on any machine. Delete the `.venv` folder entirely and `uv run` will quietly rebuild it from those records; the environment is disposable, the manifest is truth.

Prove the isolation to yourself:

```bash
uv run python -c "import requests; print(requests.__version__)"
```

prints a version number, while running the same import through a Python *outside* this folder fails with an error, because `requests` exists only in this project's environment. That failure is the feature. Every project in this curriculum starts with `uv init`, and no package is ever installed globally.

## The Legacy Stack You Will Still Meet

uv is the present, but a decade of tutorials, Stack Overflow answers, and workplace codebases speak the older dialect. You need to *recognize* these tools, even though you will rarely type them:

- **pip** is the classic package installer: `pip install requests`. It works, it is everywhere, and `uv add` supersedes it in your workflow. If a tutorial says `pip install X`, the translation is `uv add X`.
- **venv** is the built-in way to create a virtual environment manually: `python -m venv .venv` followed by an `activate` command that differs per operating system. uv creates and uses environments implicitly, so you skip the activation dance entirely.
- **requirements.txt** is the older way to record dependencies: a plain list of package names, installed with `pip install -r requirements.txt`. `pyproject.toml` plus a lockfile is its modern replacement, but you will see `requirements.txt` in the wild for years to come.
- **conda** is a separate ecosystem popular in data science, with its own installer and package format. It solved real problems in the era before wheels and uv; you only need to know it exists and that this curriculum does not use it.
- **pyenv, pipx, poetry** are earlier tools that each solved one slice of the problem (version switching, tool installs, project management). uv absorbs all three roles.

The full landscape, including how packages are built and published rather than just consumed, is Topic 28: Packaging and Distribution Landscape material. For now, recognition is enough.

## The Editor

Install [Visual Studio Code](https://code.visualstudio.com/) and, inside it, the official [Python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python) from Microsoft. This combination is free, runs everywhere, and is what most of the Python world uses. Open your `socc-playground` folder with `File > Open Folder`, and VS Code will detect the `.venv` environment automatically, giving you autocomplete and error squiggles powered by the exact packages your project has.

Two notes, then we move on. First, if you already live in another editor (Vim, Zed, a JetBrains IDE), Python support exists for all of them and nothing in this curriculum is VS Code-specific. Second, VS Code's deeper features, debugging above all, get proper treatment in Topic 20: Tooling, Testing, and Debugging; today it is just a smarter place to type.

## Verify Everything

A setup is only done when it is proven. Run this checklist from a fresh terminal:

```bash
uv --version                 # 1. uv responds
uv python list               # 2. shows 3.14 among installed versions
cd socc-playground
uv run python -c "import sys; print(sys.version)"     # 3. prints 3.14.x
uv run python -c "import requests; print('isolation works')"  # 4. package found
```

If all four print sensible output, your machine is ready for every topic that follows. If step 1 fails, your shell has not picked up uv's install location: open a new terminal, and failing that, consult the [uv installation docs](https://docs.astral.sh/uv/getting-started/installation/) for the PATH note matching your operating system.

## Key Takeaways

- A Python setup has four parts: the **interpreter** that runs code, **packages** from PyPI, **virtual environments** that isolate each project's packages, and an editor.
- **uv** manages the first three in one fast binary: `uv python install` fetches interpreters, `uv init` starts a project, `uv add` installs packages into that project's own environment.
- `pyproject.toml` is the project manifest and `uv.lock` pins exact versions; the `.venv` folder is disposable and rebuildable from them.
- One project, one environment, nothing installed globally: this habit is non-negotiable and every SOCC project follows it.
- The legacy stack (pip, venv, requirements.txt, conda) still dominates older tutorials; recognize it, translate `pip install X` to `uv add X`, and move on.
- The REPL (`uv run python`) is for experiments; `.py` files run with `uv run python file.py` are for programs you keep.

## Think About It

uv succeeded partly by refusing to be one more tool in the flowchart: it replaced the whole flowchart at once, and it came from outside the Python core team, built in a different language entirely. What does it say about an ecosystem when its best tooling arrives from outside? Rust's cargo, by contrast, shipped with the language from the start. Which approach produces better tools in the long run: official tooling that evolves slowly with the language, or an open field where outsiders can win?

## Next Topic

Topic 03: Program Structure and Execution, where the `hello.py` you just ran stops being magic: what the interpreter actually does with a source file, and what a Python program is made of.
