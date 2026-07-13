# Topic 01: Python: History, Versions, and Governance

Before you write a single line of Python, it is worth knowing what you are stepping into. Python is more than three decades old, it is run by a nonprofit foundation rather than a company, and it evolves through a public proposal process that anyone can read. Understanding this background pays off immediately: you will know which version to install, how to recognize outdated code and tutorials, and what people mean when they casually drop terms like "PEP 8" or "the GIL" in code reviews and job interviews.

## What Python Is

Python is a high-level, general-purpose programming language designed around one core bet: code is read far more often than it is written, so readability should win. Its syntax uses indentation instead of braces, reads close to plain English, and lets you express ideas in fewer lines than languages like C++ or Java.

That readability bet is a large part of why Python became the default language of AI. Researchers who are not career software engineers can pick it up quickly, and the scientific ecosystem that grew around it (NumPy, PyTorch, and the rest, covered in Chapter 2: Python for AI and Chapter 3: PyTorch and Autograd) turned it into the glue language of machine learning. Nearly every model you will train, every API you will call, and every agent you will build in this curriculum speaks Python.

## Where It Came From

Python was created by Guido van Rossum, a Dutch programmer who started it as a hobby project over the 1989 Christmas holidays and released the first version publicly in 1991. The name has nothing to do with snakes: it honors the British comedy group Monty Python, which is why official documentation is full of spam, eggs, and silly examples.

Van Rossum designed Python as a descendant of a teaching language called ABC, keeping its beginner-friendliness while making it practical for real systems work. The design philosophy was later distilled into a short document called [The Zen of Python (PEP 20)](https://peps.python.org/pep-0020/), nineteen aphorisms that still guide the language today. You can print it in any Python session by typing `import this`. Lines like "Readability counts" and "There should be one, and preferably only one, obvious way to do it" are not decoration: they explain hundreds of design decisions you will encounter throughout this chapter.

For most of its history, Python's direction was decided by van Rossum personally. The community gave him a tongue-in-cheek title: BDFL, Benevolent Dictator For Life. Keep that acronym in mind; it becomes relevant again in the governance section.

## The Version That Broke Everything

In 2008, the core team released Python 3.0, and it did something almost unheard of in programming languages: it deliberately broke backward compatibility. Python 2 code would not necessarily run on Python 3. The team judged that early design mistakes could only be fixed with a clean break. The headline changes:

- `print` went from a statement to a function: `print "hello"` became `print("hello")`
- Text and binary data were cleanly separated: strings became Unicode by default, fixing a whole class of internationalization bugs
- Integer division stopped silently truncating: `3 / 2` now returns `1.5`, with `//` for floor division

The migration was expected to take a few years. It took over a decade. Libraries had to support both versions simultaneously, companies had millions of lines of Python 2, and the community split painfully. Python 2 finally reached end of life on January 1, 2020, with a farewell at [python.org's sunset page](https://www.python.org/doc/sunset-python-2/).

Why does this ancient drama matter to you in 2026? Because the internet never forgets. You will still find Python 2 tutorials, Stack Overflow answers, and legacy codebases. The quickest tell is the print statement: if you see `print "hello"` without parentheses, the material is Python 2 and you should look elsewhere. Everything in SOCC, and effectively everything in modern AI, is Python 3.

## Python Today

Python now ships one feature release every year, each October, a cadence fixed by [PEP 602](https://peps.python.org/pep-0602/). Each release then receives roughly two years of bug fixes and three further years of security-only patches, five years of support in total. The [Python documentation by version page](https://www.python.org/doc/versions/) and [endoflife.date/python](https://endoflife.date/python) show the current support status of every release at a glance.

As of mid-2026, the landscape looks like this:

- **Python 3.14** is the current stable release line (released October 2025)
- **Python 3.15** is in development, expected October 2026
- **Python 3.10** is the oldest branch still receiving security fixes; 3.9 and earlier are dead
- Versions are written major.minor.micro: in 3.14.6, the 6 counts bug-fix updates that change no features

Two ongoing projects define Python's current direction. First, the Faster CPython effort has been speeding up the reference interpreter release by release since 3.11. Second, and more dramatically, Python 3.14 made the free-threaded build an officially supported option ([PEP 779](https://peps.python.org/pep-0779/)). This build removes the Global Interpreter Lock (GIL), a decades-old design constraint that prevented threads from running Python code truly in parallel. You do not need to understand the GIL yet; Topic 19: Concurrency and the Runtime treats it properly. For now, know that it is one of the most discussed topics in the Python world and that it is finally, gradually, going away.

## Who Decides

Python is not owned by any company. Three structures govern it:

**The Python Software Foundation (PSF).** A nonprofit founded in 2001 that holds Python's intellectual property, funds development and community grants, and runs PyCon, the main conference. Details at [python.org/psf](https://www.python.org/psf-landing/).

**The Steering Council.** In 2018, after a particularly draining community dispute over a language feature, Guido van Rossum stepped down as BDFL. The community adopted a new model ([PEP 8016](https://peps.python.org/pep-8016/)): a five-person Steering Council, elected annually by the core developers, now makes final decisions about the language.

**PEPs: Python Enhancement Proposals.** Every significant change to Python starts as a PEP, a public design document that anyone can read at [peps.python.org](https://peps.python.org/). A PEP states the problem, the proposed solution, and the alternatives considered; the community debates it and the Steering Council accepts or rejects it. PEPs are also why Python changes are so well documented historically: you have already seen PEP 20 (the Zen), PEP 602 (the release cadence), and PEP 779 (free-threading) in this topic alone.

One PEP is so famous it has become vocabulary: [PEP 8](https://peps.python.org/pep-0008/), the official style guide for Python code. When a colleague says your code "isn't PEP 8 compliant," they mean the formatting deviates from community convention. Tools that enforce it automatically appear in Topic 20: Tooling, Testing, and Debugging.

## What Version Should You Use

The practical answer for this curriculum: **the latest stable release**, which as of this writing means Python 3.14. It is fast, fully supported, and everything in this chapter works on it.

Two refinements to that answer:

1. **For your own learning and new projects**: latest stable, always.
2. **For existing projects at work**: whatever the project already pins. Production teams often stay one or two versions behind the latest while their dependencies catch up, which is why 3.12 and 3.13 remain everywhere in industry. This is normal, not neglect.

You do not need to download anything yet. Topic 02: Environment Setup installs Python the professional way, with a tool that can fetch and switch between any version on demand.

## Key Takeaways

- Python was created by Guido van Rossum, first released in 1991, and named after Monty Python, with readability as its founding principle (`import this` shows the Zen of Python).
- Python 3 (2008) deliberately broke compatibility with Python 2, which died in 2020. A `print` statement without parentheses marks a tutorial or codebase as obsolete Python 2.
- Python releases annually each October, with five years of support per release. As of mid-2026 the current line is 3.14, with 3.15 arriving in October 2026.
- The language is governed by the nonprofit PSF, a five-person elected Steering Council, and the public PEP process; PEP 8 (style) is the one you will hear about most.
- Use the latest stable release for learning and new work; respect whatever version existing projects pin.

## Think About It

Python 3's breaking changes cost the community a decade of migration pain, but the maintainers judged the cleanup worth it. JavaScript made the opposite choice: it never breaks old code, so every historical mistake still works today. Which philosophy would you choose if you maintained a language used by millions, and what does each choice cost?

## Next Topic

Topic 02: Environment Setup, where you install Python with modern professional tooling and run your first code.
