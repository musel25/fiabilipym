# Fiabilipy Diff Report — Repo B (fiabilipyg-2.8.1) vs Repo A (fiabilipy-2.7)

## Executive summary
- Repo B is a light fork focused on updating the **drawing** functionality for systems (networkx graph rendering) and minor string normalization. Core reliability algorithms and public APIs remain unchanged. (Sources: `fiabilipyg-2.8.1/fiabilipy/system.py`, `fiabilipyg-2.8.1/fiabilipy/voter.py`)
- Packaging metadata diverges: Repo B renames the distribution to **fiabilipyg** and updates setup metadata, but the runtime module version string is still `2.7`, and dependencies are no longer declared in `setup.py`. (Sources: `fiabilipyg-2.8.1/setup.py`, `fiabilipyg-2.8.1/fiabilipy/__init__.py`)

## Added features (grouped by area)

### Visualization
- **System draw updated to use graphviz_layout + labels**: `System.draw()` now uses `networkx.drawing.nx_agraph.graphviz_layout()` and `nx.draw()` with labels for components, plus explicit labels for `'E'` and `'S'`. This changes the required dependencies (pygraphviz/graphviz) and the look of graphs. (Source: `fiabilipyg-2.8.1/fiabilipy/system.py`)

## API changes (new / changed / removed)

### Added public API endpoints
- None. The public API exported from `fiabilipy/__init__.py` is unchanged (`System`, `Component`, `Voter`, `Markovprocess`). (Source: `fiabilipyg-2.8.1/fiabilipy/__init__.py`)

### Changed APIs / behavior
- **`System.draw()` behavior** changes from `nx.draw_graphviz(self._graph)` to a labeled `nx.draw()` call with graphviz layout. This can affect layout, dependency requirements (pygraphviz), and whether labels are displayed. (Source: `fiabilipyg-2.8.1/fiabilipy/system.py`)
- **`Voter` name formatting** changes from using a Unicode en dash to a standard hyphen in the generated component name string. This can affect string comparisons/serialization in user code. (Source: `fiabilipyg-2.8.1/fiabilipy/voter.py`)

### Removed / breaking changes
- No functional removals in code, but **dependency declarations were removed** from `setup.py` (Repo B) which can break installation expectations compared to Repo A. (Sources: `fiabilipy-2.7/setup.py`, `fiabilipyg-2.8.1/setup.py`)

## Internal refactors (what moved / renamed)
- None in code structure. File layout and module names are identical.

## Tests & docs changes
- Tests appear unchanged (`test_system.py`, `test_markov.py`). (Sources: `fiabilipy-2.7/fiabilipy/test_system.py`, `fiabilipyg-2.8.1/fiabilipy/test_system.py`, `fiabilipy-2.7/fiabilipy/test_markov.py`, `fiabilipyg-2.8.1/fiabilipy/test_markov.py`)
- Repo B does not include a README at the root (Repo A has a short `README`). (Sources: `fiabilipy-2.7/README`, directory listing from `fiabilipyg-2.8.1/`)

## Migration notes (A → B)
- **Graph drawing**: If you rely on `System.draw()`, ensure `pygraphviz` (or compatible Graphviz bindings) and Graphviz are installed; the new implementation uses `graphviz_layout`. (Source: `fiabilipyg-2.8.1/fiabilipy/system.py`)
- **Packaging**: The distribution name is now `fiabilipyg` (not `fiabilipy`), and its setup metadata does not declare dependencies. Consumers may need to install numpy/scipy/sympy/networkx manually. (Sources: `fiabilipyg-2.8.1/setup.py`, `fiabilipy-2.7/setup.py`)
- **Version mismatch**: `fiabilipy.__version__` still reports `2.7` in Repo B, which may confuse runtime checks or logging. (Source: `fiabilipyg-2.8.1/fiabilipy/__init__.py`)
