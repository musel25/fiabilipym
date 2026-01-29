# Changes made to fiabilipy (latest repo: fiabilipyg-2.8.1)

Goal: Modernize Python-2-era code to run on Python 3.8–3.12 without changing public API or mathematical behavior.

## Summary of edits
All changes are mechanical Python 2 → Python 3 compatibility fixes. No algorithms, formulas, or public APIs were altered.
Added `pygraphviz` dependency to ensure `System.draw()` graph visualization works with Graphviz layouts.

## File-by-file details

### `fiabilipyg-2.8.1/fiabilipy/system.py`
- Imported `reduce` from `functools` so the existing `reduce(...)` calls work in Python 3.
- Replaced `collections.Iterable` with `collections.abc.Iterable` (Python 3 location).
- Replaced `xrange(...)` with `range(...)` in code and doctest examples.

Why: `xrange` and `collections.Iterable` are Python 2-only; `reduce` moved to `functools` in Python 3.

### `fiabilipyg-2.8.1/fiabilipy/voter.py`
- Replaced `xrange(...)` with `range(...)`.

Why: `xrange` is Python 2-only.

### `fiabilipyg-2.8.1/fiabilipy/markov.py`
- Replaced `xrange(...)` with `range(...)` in code and doctest examples.
- Updated numpy indexing line from `self.matrix[xrange(...), xrange(...)]` to `self.matrix[range(...), range(...)]`.

Why: `xrange` is Python 2-only; range is the Python 3 equivalent and works with numpy indexing.

### `fiabilipyg-2.8.1/fiabilipy/test_system.py`
- Replaced `dict.iteritems()` with `dict.items()`.
- Replaced `xrange(...)` with `range(...)`.

Why: `iteritems` and `xrange` are Python 2-only; Python 3 uses `items()` and `range()`.

### `fiabilipyg-2.8.1/fiabilipy/test_markov.py`
- Replaced `dict.iteritems()` with `dict.items()`.
- Replaced `xrange(...)` with `range(...)`.

Why: `iteritems` and `xrange` are Python 2-only.

## Notes on behavior
- These changes are mechanical; numerical behavior and APIs are unchanged.
- Doctest example text was updated only where it referenced `xrange`.
