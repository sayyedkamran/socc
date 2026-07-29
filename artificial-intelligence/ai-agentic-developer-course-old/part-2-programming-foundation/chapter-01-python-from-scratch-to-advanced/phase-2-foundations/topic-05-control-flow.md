# Topic 05: Control Flow

Topic 04: Variables and Core Types left the program with named values and a promise: values would stop sitting still and start steering. That steering is **control flow**, the machinery that lets a program make decisions, repeat work, and choose paths. Until now, every script has been a straight line: statement one, statement two, done. Real programs branch and loop, and the shape of that branching and looping is most of what makes code readable or unreadable.

Python's control flow is small: one conditional statement, two loops, and one newcomer, the `match` statement. But each carries design decisions worth understanding rather than memorizing, and one of them, the `for` loop, works so differently from its namesake in C-family languages that arriving programmers regularly use it for years without noticing what it actually does.

## Conditionals

The `if` statement needs no parentheses around its condition and no braces around its body; the colon and indentation from Topic 03: Program Structure and Execution do the delimiting:

```python
if temperature > 30:
    print("hot")
elif temperature > 15:
    print("mild")
else:
    print("cold")
```

`elif` chains evaluate top to bottom and stop at the first true condition, so order encodes priority. There is no separate switch statement for most of Python's history; the `elif` chain was the tool, and for simple cases it still is. The `match` statement, covered at the end of this topic, now handles the structured cases.

For choosing a value rather than an action, Python has a conditional expression, with the condition sitting unusually in the middle:

```python
label = "hot" if temperature > 30 else "not hot"
```

Read it aloud as written ("label is hot, if the temperature is over 30, else not hot") and the ordering makes sense. Use it for short, single choices; the moment it nests, switch back to a full `if` statement.

## Truthiness, For Real This Time

Topic 04: Variables and Core Types previewed truthiness; conditions are where it earns its keep. Every Python object is either truthy or falsy, and the falsy inventory is short enough to memorize outright:

- `False` and `None`
- Every numeric zero: `0`, `0.0`
- Every empty container: `""`, `[]`, `{}`, `()`, `set()`

Everything else is truthy. This is why idiomatic Python writes `if name:` rather than `if len(name) > 0:`, and `if not results:` rather than `if len(results) == 0:`. The condition asks the object directly: is there something here?

The operators `and` and `or` interact with truthiness in a way that surprises people: they do not return `True` or `False`. They return **one of their operands**, evaluated lazily:

- `a or b` returns `a` if `a` is truthy, otherwise `b`
- `a and b` returns `a` if `a` is falsy, otherwise `b`

Both **short-circuit**: if the left operand decides the answer, the right operand is never evaluated at all. This enables two everyday patterns:

```python
>>> username = "" or "guest"       # fallback default
>>> username
'guest'
>>> config and config.reload()      # guarded call: reload() only runs if config is truthy
```

The `or` default pattern carries a classic trap worth flagging now: it replaces *every* falsy value, not just missing ones. If `0` or `""` is a legitimate input, `value or default` silently discards it, and the correct test is `value if value is not None else default`. Keep that distinction in a back pocket; it returns with real consequences in Topic 09: Functions, where default arguments raise exactly this issue.

## The `while` Loop

`while` repeats its body as long as its condition holds; it is the loop for "keep going until something happens", when the number of repetitions is not known in advance:

```python
attempts = 0
while attempts < 3:
    password = input("Password: ")
    if password == secret:
        break
    attempts += 1
```

Two statements steer from inside any loop. `break` exits the loop immediately; `continue` abandons the current pass and jumps to the next one. Both apply to the *innermost* enclosing loop only. A common shape is the intentionally infinite `while True:` loop with a `break` at the exit condition; far from being a hack, it is the standard idiom when the natural exit point falls in the middle of the body rather than at the top.

## The `for` Loop Is Not a Counter

Here is the recalibration this topic exists for. In C, Java, or Rust's C-style ancestors, `for` manages a counter: initialize, test, increment. Python's `for` does something categorically different: it walks through the items of an **iterable**, an object capable of producing its elements one at a time. Strings, lists, files, dictionaries: all iterable, all directly walkable:

```python
for char in "abc":
    print(char)
```

There is no index anywhere. When Python programmers want numbers to loop over, they reach for `range`, which is itself just an iterable that produces integers:

```python
for i in range(5):        # 0, 1, 2, 3, 4
    print(i)
```

But needing the index at all is rarer than newcomers expect, because two built-ins cover the classic reasons for wanting one. `enumerate` yields position and item together, and `zip` walks multiple iterables in lockstep:

```python
for i, line in enumerate(lines, start=1):
    print(i, line)

for name, score in zip(names, scores):
    print(name, score)
```

The test of Pythonic loop style: if you find yourself writing `for i in range(len(items)):` and then indexing with `items[i]`, one of these three tools almost certainly says it better. How iteration actually works underneath, the protocol that lets any object, including ones you write, plug into `for`, is one of Python's deepest ideas, and it gets full treatment in Topic 14: The Python Data Model and Idioms.

## The Loop `else`

Both loops accept an `else` clause, and its meaning trips nearly everyone once: the `else` body runs when the loop finishes **without hitting `break`**. Read it as "no break happened":

```python
for candidate in servers:
    if candidate.responds():
        print("using", candidate)
        break
else:
    print("no server responded")
```

The search-and-fallback pattern above is the clause's natural habitat: try each option, `break` on success, and the `else` becomes the failure path, with no flag variable needed. It is a genuinely useful construct wrapped in a genuinely confusing keyword choice; many experienced Python developers simply avoid it, and using a flag variable instead is a defensible style decision. Know it so you can read it.

## Structural Pattern Matching

Python 3.10 added `match`, and calling it a switch statement undersells it. `case` clauses do not just compare values; they **destructure** them, testing shape and binding parts in one motion:

```python
match command.split():
    case ["quit"]:
        running = False
    case ["load", filename]:
        load(filename)
    case ["move", x, y] if x != y:
        move(x, y)
    case _:
        print("unknown command")
```

Reading the pieces: a list literal pattern like `["load", filename]` matches any two-element sequence whose first element equals `"load"`, and binds the second to `filename`. Bare names in patterns are **capture patterns** (they bind, not compare), the `if` after a pattern is a **guard** adding an arbitrary condition, and `_` is the wildcard that matches anything. Patterns can also destructure dictionaries and, later, class instances.

When to prefer `match` over an `elif` chain: when the branching is on the *structure* of data, especially parsed input, rather than on unrelated conditions. A chain of `elif` clauses each doing its own indexing and length-checking collapses into cases that read like the data they match. When the branches test unrelated conditions (`temperature`, then `humidity`, then `wind`), a plain `elif` chain remains the right tool.

## Shape Matters: Nesting and Guard Clauses

Control flow is where code readability is won or lost, and the primary signal is **nesting depth**. Every level of indentation is a condition the reader must hold in their head. Compare:

```python
def process(order):
    if order is not None:
        if order.items:
            if order.paid:
                ship(order)
```

against the same logic written with **guard clauses**, early exits that handle the disqualifying cases first:

```python
def process(order):
    if order is None:
        return
    if not order.items:
        return
    if not order.paid:
        return
    ship(order)
```

The second version reads as a checklist, keeps the main action at the left margin, and scales as conditions accumulate. This shape, reject early and keep the happy path flat, is one of the highest-value habits in all of programming, and it costs nothing but the willingness to `return` more than once.

## Putting It All Together

A number-guessing game is small enough to fit on one screen and exercises every construct in this topic: a `while True` loop, `break` and `continue`, truthiness, an `elif` chain, and a `match` on parsed input.

```python
# guess.py
import random

def main():
    secret = random.randint(1, 100)
    tries = 0
    while True:
        raw = input("Guess (or 'quit'): ").strip()
        if not raw:                      # truthiness: empty string is falsy
            continue
        match raw.split():
            case ["quit"]:
                print("The number was", secret)
                break
            case [word] if word.isdigit():
                guess = int(word)
            case _:
                print("Enter a number or 'quit'.")
                continue
        tries += 1
        if guess < secret:
            print("Higher.")
        elif guess > secret:
            print("Lower.")
        else:
            print(f"Got it in {tries} tries.")
            break

if __name__ == "__main__":
    main()
```

Run it, then break it on purpose: remove the `strip()`, feed it garbage, delete the guard on the digit case. Watching where the control flow catches each failure, and where it stops catching them, teaches more than reading ever will.

## Key Takeaways

- `if`/`elif`/`else` chains evaluate top to bottom and stop at the first match; order encodes priority. Conditional expressions (`x if cond else y`) handle single-value choices.
- The falsy values are `False`, `None`, numeric zeros, and empty containers; everything else is truthy. Idiomatic conditions ask objects directly: `if name:`, `if not results:`.
- `and` and `or` return **operands**, not booleans, and short-circuit. The `value or default` pattern discards all falsy values, so use `is not None` when zero or empty string is legitimate.
- `while` handles unknown repetition counts; `while True` with `break` is a standard idiom, not a hack. `break` exits the innermost loop; `continue` skips to its next pass.
- Python's `for` walks iterables, not counters. Reach for `range`, `enumerate`, and `zip` instead of manual indexing; `for i in range(len(items))` is the anti-pattern that signals one of them is missing.
- A loop's `else` clause runs only if no `break` fired; its natural use is search-with-fallback.
- `match` destructures data: sequence patterns bind elements, guards add conditions, `_` catches the rest. Prefer it over `elif` when branching on the shape of data.
- Fight nesting depth with guard clauses: reject early, return often, keep the happy path flat.

## Think About It

Python's `for` loop cannot express "loop while incrementing a counter however I like"; you get iteration over an iterable, or you build the behavior manually with `while`. That is a real reduction in power compared to C's three-part `for`, and it was chosen deliberately. What does a language gain by making the common case (walk these items) effortless and the rare case (exotic counter manipulation) slightly awkward? And notice the mirror image: C-family languages make "walk these items" the case requiring boilerplate, and off-by-one errors are among their most common bugs. Is that a coincidence? As you form an answer, consider who reads code more often: the machine executing it, or the humans maintaining it.

## Next Topic

Topic 06: Data Structures. Loops need something to walk over, and so far that has been strings and `range`. Python's real power tools are its built-in collections: lists, tuples, dictionaries, and sets, each with its own mutability story (a thread picked up directly from Topic 04: Variables and Core Types) and its own answer to the question of what a program should reach for when it needs to hold many things at once.
