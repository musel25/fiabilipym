# Fiabilipy 2.7 — Technical Audit (Repo A)

## Overview
- **What it is**: A small Python reliability engineering library for teaching, built around constant-rate components, reliability block diagrams, and Markov processes. The package exposes four core classes: `Component`, `Voter`, `System`, and `Markovprocess`. (Sources: `fiabilipy-2.7/fiabilipy/__init__.py`, `fiabilipy-2.7/fiabilipy/component.py`, `fiabilipy-2.7/fiabilipy/voter.py`, `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipy-2.7/fiabilipy/markov.py`)
- **Packaging**: Python 2 era `distutils` package with dependencies declared in `setup.py` and `setup.cfg` (numpy, scipy, sympy, networkx). (Sources: `fiabilipy-2.7/setup.py`, `fiabilipy-2.7/setup.cfg`)

## Repo discovery (top-level tree, depth ~3)

### Repo A — `fiabilipy-2.7`
```
fiabilipy-2.7
├── PKG-INFO
├── README
├── setup.cfg
├── setup.py
└── fiabilipy
    ├── __init__.py
    ├── component.py
    ├── markov.py
    ├── system.py
    ├── test_markov.py
    ├── test_system.py
    └── voter.py
```

### Repo B — `fiabilipyg-2.8.1`
```
fiabilipyg-2.8.1
├── PKG-INFO
├── setup.cfg
├── setup.py
├── fiabilipy
│   ├── __init__.py
│   ├── component.py
│   ├── markov.py
│   ├── system.py
│   ├── test_markov.py
│   ├── test_system.py
│   └── voter.py
└── fiabilipyg.egg-info
    ├── PKG-INFO
    ├── SOURCES.txt
    ├── dependency_links.txt
    └── top_level.txt
```

## Architecture map (modules and responsibilities)
- `fiabilipy/__init__.py`: Public API exports and package version string. Exposes `System`, `Component`, `Voter`, `Markovprocess`. (Source: `fiabilipy-2.7/fiabilipy/__init__.py`)
- `fiabilipy/component.py`: Core component model with constant failure/repair rates; computes reliability/maintainability/availability and MTTF/MTTR. (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `fiabilipy/voter.py`: `Voter` combinator for M-out-of-N identical components; computes system-level metrics for a voter plus its own failure/repair rates. (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `fiabilipy/system.py`: Reliability block diagram (RBD) system built on a directed graph; computes metrics via inclusion–exclusion over success paths; minimal cuts; fault tree output; graph drawing. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `fiabilipy/markov.py`: Continuous-time Markov chain (CTMC) model over component working/failed states; computes probability vector via matrix exponential. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- Tests (in-package): `test_system.py`, `test_markov.py`. (Sources: `fiabilipy-2.7/fiabilipy/test_system.py`, `fiabilipy-2.7/fiabilipy/test_markov.py`)

## Public API (with signatures + file locations)

### `Component` (constant-rate component)
- `Component.__init__(name, lambda_, mu=0, initialy_avaible=True)` (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `Component.reliability(t)` (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `Component.maintainability(t)` (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `Component.availability(t)` (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `Component.mttf` (property) (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `Component.mttr` (property) (Source: `fiabilipy-2.7/fiabilipy/component.py`)

### `Voter` (M-out-of-N voter)
- `Voter.__init__(component, M, N, lambda_=0, mu=0, initialy_avaible=True)` (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `Voter.reliability(t)` (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `Voter.maintainability(t)` (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `Voter.availability(t)` (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `Voter.mttf` (property) (Source: `fiabilipy-2.7/fiabilipy/voter.py`)
- `Voter.mttr` (property) (Source: `fiabilipy-2.7/fiabilipy/voter.py`)

### `System` (RBD-based system)
- `System.__init__(graph=None)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.__setitem__(component, successors)` (graph construction) (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.__getitem__(component)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.__delitem__(component)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.copy()` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.components` (property) (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.availability(t)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.reliability(t)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.maintainability(t)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.mttf` (property) (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.mttr` (property) (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.successpaths` (property) (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.findallpaths(start='E', end='S')` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.minimalcuts(order=1)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.faulttreeanalysis(output=None, order=2)` (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- `System.draw()` (Source: `fiabilipy-2.7/fiabilipy/system.py`)

### `Markovprocess` (CTMC over component states)
- `Markovprocess.__init__(components, initstates)` (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- `Markovprocess.value(t, statefunc=None)` (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- `Markovprocess.draw(output=None)` (Source: `fiabilipy-2.7/fiabilipy/markov.py`)

## Key algorithms / logic

### Constant-rate component model
- `Component.reliability(t)` uses `exp(-lambda * t)`; `maintainability(t)` uses `1 - exp(-mu * t)`; `availability(t)` uses the closed-form two-state availability formula with initial state control (`initialy_avaible`). (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- `mttf` and `mttr` are analytic integrals for constant-rate exponential failure/repair, computed as `1 / lambda_` and `1 / mu` respectively. (Source: `fiabilipy-2.7/fiabilipy/component.py`)

### Reliability block diagram (RBD) system
- **Graph-based structure**: The system is a `networkx.DiGraph` with sentinel nodes `'E'` (start) and `'S'` (end); components are nodes in between. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Success paths enumeration**: `successpaths` uses `networkx.all_simple_paths` to enumerate paths from `'E'` to `'S'`. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Inclusion–exclusion reliability**: `System._probabilitiescomputation()` computes reliability/availability/maintainability by inclusion–exclusion over all success paths (`ALLSUBSETS` over paths). (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Minimal cuts**: `minimalcuts(order)` builds incidence matrices over paths/components to generate minimal cuts up to a given order. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Fault tree output**: `faulttreeanalysis()` emits Graphviz DOT for a tree built from minimal cuts. (Source: `fiabilipy-2.7/fiabilipy/system.py`)

### Markov process model
- **CTMC state space**: For N components, it constructs a `2^N × 2^N` rate matrix and evolves the state vector via `scipy.linalg.expm`. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- **State selection**: `value(t, statefunc)` sums probabilities of states for which a provided boolean function returns truthy, using cached state lists in `_states`. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)

## Data flow (inputs → outputs)
- **Component-level**: `Component` takes scalar failure/repair rates and returns symbolic or numeric functions for reliability/availability/maintainability. (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- **System-level**:
  1) User builds graph via `System.__setitem__` (links components). (Source: `fiabilipy-2.7/fiabilipy/system.py`)
  2) `successpaths` enumerates all simple paths from `'E'` to `'S'`. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
  3) Inclusion–exclusion combines component metrics along each path to compute overall reliability/availability/maintainability. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
  4) `mttf` / `mttr` integrate symbolic expressions. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Markov process**:
  1) Build CTMC rate matrix from component rates. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
  2) Evolve state probabilities with `expm(t * Q)`. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
  3) Sum a subset of states to produce availability-like measures. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)

## Tests & quality notes
- **Existing tests**:
  - `test_system.py` covers success paths, minimal cuts (orders 1 and 2), MTTF value comparisons, graph construction, and cache invalidation. (Source: `fiabilipy-2.7/fiabilipy/test_system.py`)
  - `test_markov.py` compares Markov-based availability to RBD availability for several system structures. (Source: `fiabilipy-2.7/fiabilipy/test_markov.py`)
- **Gaps**:
  - No dedicated tests for `Component` or `Voter` methods (reliability/availability/maintainability, `mttf`/`mttr`). (Sources: `fiabilipy-2.7/fiabilipy/component.py`, `fiabilipy-2.7/fiabilipy/voter.py`)
  - No tests for `faulttreeanalysis()` output format or `draw()` rendering paths. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
  - No tests for `Markovprocess.draw()` output or for state indexing assumptions (see issues below). (Source: `fiabilipy-2.7/fiabilipy/markov.py`)

## Performance & dependencies
- **Complexity**:
  - System metrics use inclusion–exclusion over all subsets of success paths → exponential in the number of paths. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
  - Markov matrix is `2^N × 2^N`; `expm` is expensive and quickly becomes infeasible as N grows. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- **Dependencies**: numpy, scipy, sympy, networkx (declared in `setup.py` / `setup.cfg`). (Sources: `fiabilipy-2.7/setup.py`, `fiabilipy-2.7/setup.cfg`)

## Documentation quality
- README is minimal (high-level, no usage examples beyond a short description). (Source: `fiabilipy-2.7/README`)
- Docstrings are thorough for most public methods, with examples. However, some Markov process docs mention non-existent methods/parameters (see issues below). (Source: `fiabilipy-2.7/fiabilipy/markov.py`)

## Known issues / opportunities

### Potential bugs / footguns
- **Markov state encoding inconsistencies**: The Markov documentation implies that a bit value of `1` means “working” (see the example loop in the docstring), but `_initmatrix()` assumes a fixed index ordering and assigns failure/repair rates based on index ordering rather than on bit direction. `_computestates()` also reverses the index mapping with `nsquared - 1 - x`. This mix of conventions suggests a likely mismatch between docs, state encoding, and the CTMC rate matrix. This should be clarified and tested explicitly. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- **Docstring mismatches**: The Markov docstring refers to `computestates()` (public) and `value(..., states=...)`, but only `_computestates()` exists and `value()` takes `statefunc` instead. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- **Divide-by-zero risks**: `Component.mttf` and `Component.mttr` do not guard against `lambda_ == 0` or `mu == 0` → division by zero. (Source: `fiabilipy-2.7/fiabilipy/component.py`)
- **Python 2-only constructs**: `xrange`, `collections.Iterable`, and reliance on `reduce` as a builtin make the code Python-2-specific. (Sources: `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipy-2.7/fiabilipy/markov.py`, `fiabilipy-2.7/fiabilipy/voter.py`, `fiabilipy-2.7/fiabilipy/test_*`)
- **`System.__setitem__` accepts any iterable**: Passing a string (other than `'S'`) can lead to unexpected iteration over characters. (Source: `fiabilipy-2.7/fiabilipy/system.py`)

### Opportunities
- Provide explicit API for building common structures (series, parallel, M-out-of-N, etc.) to reduce manual graph wiring. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- Add tests for `Component`, `Voter`, `faulttreeanalysis`, and Markov state encoding. (Sources: `fiabilipy-2.7/fiabilipy/component.py`, `fiabilipy-2.7/fiabilipy/voter.py`, `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipy-2.7/fiabilipy/markov.py`)
- Consider performance alternatives to inclusion–exclusion for large path counts (e.g., minimal path/cut sets, BDD-based reliability). (Source: `fiabilipy-2.7/fiabilipy/system.py`)
