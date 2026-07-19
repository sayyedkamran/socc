# Topic 04: Variables and Core Types

Topic 03: Program Structure and Execution ended with a pipeline: source becomes bytecode, and the PVM executes it one instruction at a time. But a pipeline is only interesting for what flows through it. What flows through Python's pipeline is values, and values need names. This topic is about how Python connects the two, and the answer contains the single most consequential idea in the language: **every value is an object, and a variable is just a label pointing at one**.

If that sounds like a philosophical footnote, it is not. This one idea explains why `is` and `==` are different operators, why passing a huge data structure to a function costs nothing, why two variables can mysteriously change together, and why a `TypeError` mentions the value's type but never the variable's. Get this model right now and half of the "Python gotchas" you will ever meet dissolve into obvious consequences.

## Names Are Labels, Not Boxes

In many languages, a variable is a box: a region of memory with a name stamped on it, and assignment copies a value into the box. Python does not work this way. In Python, the object exists somewhere on its own, and assignment ties a **name** to it, like a label on a string attached to a balloon.

```python
>>> x = 500
>>> y = x
```

No copying happened here. Python created one integer object, `500`, bound the name `x` to it, and then bound the name `y` to the same object. You can verify this with `id()`, which returns a number uniquely identifying an object for its lifetime (in CPython, its memory address):

```python
>>> id(x)
140704551742864
>>> id(y)
140704551742864
```

Same object, two labels. The `is` operator asks exactly this question: do these two names point at the very same object? By contrast, `==` asks whether two objects have equal values, which is a different and usually more useful question. Two distinct balloons can be the same color.

```python
>>> a = [1, 2, 3]
>>> b = [1, 2, 3]
>>> a == b
True
>>> a is b
False
```

If you are coming from a language like C, Java, or Rust, recalibrate here: in those languages a variable is (or can be) real storage that assignment fills. A Python name is closer to a reference-counted smart pointer: another reference to shared data, with a count ticking up behind the scenes. Every Python object carries such a count, and when the last name (or container slot) referring to it goes away, the object is reclaimed. There is no declared storage, no ownership, and no move semantics: just objects and any number of labels on them. Topic 25: Memory and Performance returns to the machinery; for now the model is what matters.

## Every Value Is an Object, and Types Live on Objects

The second half of the big idea: in Python, the **type belongs to the object, not to the name**. A name is typeless. It is a label, and a label can be peeled off one balloon and stuck on another of a completely different kind:

```python
>>> x = 42
>>> type(x)
<class 'int'>
>>> x = "forty-two"
>>> type(x)
<class 'str'>
```

Nothing about `x` changed; `x` is just a name. What changed is which object it points to. This is **dynamic typing**: type checks happen at run time, against objects, at the moment an operation is attempted. `"2" + 2` fails not because a declaration was violated but because the `str` object on the left, asked at run time to add itself to an `int`, refuses.

This is a genuine trade against statically typed languages. A compiler for C, Java, or Rust proves before the program runs that every operation is type-correct; Python discovers type errors only on the code path that actually executes, possibly in production. What Python buys with that risk is flexibility: functions that work on anything with the right behavior (a preview of duck typing, treated properly in Topic 14: The Python Data Model and Idioms), a REPL where you can rebind anything, and no type ceremony while sketching. Later, Topic 15: Type Hints shows how modern Python claws back much of the compiler's safety without giving up the dynamism.

Because everything is an object, everything carries behavior. Even a bare integer has methods:

```python
>>> (255).bit_length()
8
>>> (3.75).is_integer()
False
```

There are no "primitive" types in the C or Java sense. `int` is a class, `42` is an instance, and the uniformity runs all the way down: functions, modules, classes themselves, all objects, all labelable. That uniformity is a large part of what Topic 14: The Python Data Model and Idioms will exploit.

## The Core Types

Python ships a small set of scalar types that appear in essentially every program. Each has one or two properties worth internalizing now.

**`int`: arbitrary precision.** Python integers do not overflow. There is no choosing between 32-bit and 64-bit sizes, no wrapping, no undefined behavior at the edges; an `int` grows to whatever size the value needs.

```python
>>> 2 ** 200
1606938044258990275541962092341162602522202993782792835301376
```

The cost is that a Python `int` is a full heap object (28 bytes for a small one, more as it grows, observable via `sys.getsizeof`), not a machine word. The benefit is that the whole category of integer overflow bugs, a real hazard in lower-level languages, simply does not exist.

**`float`: IEEE 754 double, with everything that implies.** Python floats are ordinary 64-bit hardware doubles, the same ones nearly every language uses, so they inherit the same famous behavior:

```python
>>> 0.1 + 0.2
0.30000000000000004
```

This is not a Python bug; it is binary floating point being honest. Values like 0.1 have no exact binary representation, the same way 1/3 has no exact decimal one. The practical rules: never compare floats with `==` (use `math.isclose`), and never use floats for money (the standard library's `decimal` module exists for that).

**`bool`: two singletons, and secretly integers.** `True` and `False` are the only two instances of `bool`, and `bool` is, surprisingly, a subclass of `int` with values 1 and 0:

```python
>>> True + True
2
>>> isinstance(True, int)
True
```

This is a historical artifact (booleans were added to Python long after integers), but it has one genuinely useful consequence: `sum()` over a sequence of conditions counts how many were true, a pattern you will see constantly in data code.

**`str`: immutable text.** Strings are sequences of Unicode characters, and they cannot be changed in place: every "modification" builds a new string object. For now, know that they exist, that they are immutable, and that quoting style (single or double) does not matter. Strings are rich enough to deserve their own treatment, and they get it in Topic 07: Strings and Regular Expressions.

**`None`: the absence of a value, as a value.** `None` is a singleton object meaning "nothing here": the default return of functions that return nothing, the conventional placeholder for "not provided." Because it is a singleton, the idiomatic test is identity, not equality: `if result is None:`. Some languages (Rust's `Option`, Kotlin's nullable types) build a compiler-enforced fence around absence; `None` is a polite convention instead. Nothing stops you from forgetting the check, and the resulting `AttributeError: 'NoneType' object has no attribute ...` is Python's version of the null-pointer stumble.

## Rebinding versus Mutation, and the Aliasing Trap

With the label model in hand, a crucial distinction snaps into focus. There are two completely different ways a "variable" can appear to change:

- **Rebinding** moves the label: `x = x + 1` builds a new object and points `x` at it. The old object is untouched (and reclaimed if nothing else references it).
- **Mutation** changes the object itself, in place, while every label on it stays put: `my_list.append(4)`.

Whether mutation is even possible depends on the object's type. `int`, `float`, `bool`, `str`, and `None` are **immutable**: no operation changes them in place, ever. Lists, dictionaries, and sets, which Topic 06: Data Structures covers, are **mutable**. And the interaction between mutability and the label model produces the most important gotcha in beginner Python, **aliasing**:

```python
>>> a = [1, 2, 3]
>>> b = a          # two labels, one list
>>> b.append(4)
>>> a
[1, 2, 3, 4]
```

`a` "changed" without being touched, because `a` and `b` were never two lists; they were two names for one list. Immutable types are immune to this by construction: if the object cannot change, sharing it is always safe, which is exactly why Python can hand the same integer object to a thousand names without anyone noticing or caring.

Hold on to this distinction; it is doing quiet work everywhere. When Topic 09: Functions arrives, the perennially confusing question "does Python pass by value or by reference?" will have a clean answer: neither. Python passes object references by value, which is a terse way of saying that the function receives another label on the same balloon. Mutation through that label is visible to the caller; rebinding it is not.

## Identity Surprises: Interning

Armed with `is`, curious learners immediately discover something odd:

```python
>>> a = 7
>>> b = 7
>>> a is b
True
>>> a = 700
>>> b = 700
>>> a is b
False
```

Why are two sevens the same object but two seven-hundreds are not? Because CPython **interns** small integers (the range -5 to 256), keeping a single shared object for each and handing out labels to it, since small integers are used constantly and are immutable, so sharing is free. Larger integers are created fresh as needed. Short strings that look like identifiers get similar treatment.

The lesson is not to memorize the ranges (they are implementation details and can differ between REPL and script). The lesson is the rule the surprise teaches: **use `==` for value comparisons, always, and reserve `is` for singletons like `None`**. Code that accidentally works because of interning is a bug waiting for a bigger number.

## Conversions and a First Taste of Truthiness

The type names double as conversion functions: `int("42")`, `float(3)`, `str(255)`, `bool(0)`. Conversions are always explicit constructions of new objects; Python never silently converts a string to a number the way some scripting languages do (the numeric tower is the one exception: `int` promotes to `float` in mixed arithmetic, so `1 + 2.5` is `3.5`).

`bool()` applied to arbitrary objects reveals a design choice called **truthiness**: every Python object is either truthy or falsy. Zero, empty string, and `None` are falsy; essentially everything else is truthy. This matters because conditions do not require booleans, so `if name:` is idiomatic Python for "if the string is non-empty." The full picture, including how truthiness interacts with `and`, `or`, and containers, belongs to Topic 05: Control Flow.

## Watching the Model in the REPL

The whole model in this topic is inspectable. A five-minute REPL session makes it concrete; run each line and predict the output before pressing Enter.

```python
>>> import sys
>>> x = 10
>>> y = x
>>> x is y                    # one object, two labels
True
>>> x = x + 1                 # rebinding: x moves to a new object
>>> x, y
(11, 10)
>>> big = 10 ** 100
>>> sys.getsizeof(big)        # ints grow with their value
72
>>> sys.getrefcount(None)     # thousands of labels on one singleton
4883
>>> t = True
>>> t + 41                    # bool is an int in a costume
42
```

The `sys.getrefcount(None)` line is the label model made visible at scale: one `None` object, referenced from thousands of places across the interpreter, perfectly safe because it can never change.

## Key Takeaways

- A Python variable is a **name bound to an object**, not a box holding a value. Assignment binds; it never copies.
- `is` compares identity (same object), `==` compares value (equal objects). Use `==` everywhere except singleton checks like `x is None`.
- **Types live on objects, not names**: names can rebind to anything, and type errors surface at run time. If you come from a statically typed language, think "every binding is a shared reference, checked when used."
- Core types to know cold: `int` (arbitrary precision, never overflows), `float` (IEEE 754 double, so `0.1 + 0.2 != 0.3`), `bool` (a subclass of `int`), `str` (immutable text), `None` (the absence singleton).
- **Rebinding** moves a label; **mutation** changes the object under every label. Immutable types cannot be mutated, which makes sharing them always safe.
- **Aliasing**, two names on one mutable object, is the mechanism behind most "it changed by itself" bugs, and behind Python's cheap passing of huge objects.
- CPython interns small integers and some strings; treat any `is` result on numbers or strings as an implementation accident, not a guarantee.
- Conversions are explicit (`int()`, `str()`, `float()`); truthiness lets any object stand in a condition, with the details coming in Topic 05: Control Flow.

## Think About It

Python chose "names label shared objects." Other languages made the opposite bet: in an ownership-based language like Rust, a binding owns its value, and the compiler tracks who can touch it. Each choice ripples outward. Ownership demands lifetimes, borrowing rules, and explicit cloning; Python needs none of that, but inherits aliasing bugs, reference counting overhead, and no compile-time guarantee that `None` is handled. Here is the question worth sitting with: could a language get both? What would it have to give up? As a hint, consider what the label model makes trivially easy that ownership makes hard (a REPL where everything is rebindable, objects freely shared across data structures) and what ownership makes trivially easy that labels make hard (knowing, at compile time, that nobody else can mutate what you hold).

## Next Topic

Topic 05: Control Flow, where values stop sitting still and start steering the program: conditions built on the truthiness introduced here, loops that walk sequences, and the structural pattern matching that gives Python's `match` statement a power most learners do not expect from a scripting language.
