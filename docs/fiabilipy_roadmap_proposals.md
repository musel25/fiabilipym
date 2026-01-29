# Fiabilipy Roadmap Proposals

## Principles (what to optimize for)
- **Correctness first**: Clarify state encoding, fix doc inconsistencies, and add regression tests around reliability/availability formulas. (Sources: `fiabilipy-2.7/fiabilipy/markov.py`, `fiabilipy-2.7/fiabilipy/system.py`)
- **Pedagogical clarity**: The library is education-focused; APIs should be explicit and examples should map cleanly to formulas. (Source: `fiabilipy-2.7/README`)
- **Scalability within reason**: Keep analytic methods for small systems, but add approximate/simulation paths for larger models. (Sources: `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipy-2.7/fiabilipy/markov.py`)
- **Modern packaging**: Standardize distribution metadata and dependencies to reduce install friction. (Sources: `fiabilipy-2.7/setup.py`, `fiabilipyg-2.8.1/setup.py`)

## Feature proposals (prioritized)

### 1) Markov state encoding fix + explicit state mapping (Priority: P0)
- **Problem statement / user story**: As a user, I need confidence that Markov-based results match the system model; current state encoding and docs are inconsistent and likely invert failure/repair semantics. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
- **Proposed API**:
  - `Markovprocess.state_index(bits: Sequence[int]) -> int`
  - `Markovprocess.index_state(i: int) -> Tuple[int, ...]`
  - `Markovprocess.value(t, statefunc=None, states=None)` (support explicit `states` list, aligning with docstrings)
- **Implementation sketch**:
  - Centralize state ordering in `markov.py`; define a single mapping (e.g., bit=1 means working). Use that mapping in `_initmatrix()`, `_computestates()`, and docs. (Files: `fiabilipy/markov.py`)
  - Update `_initmatrix()` to set lambda vs mu based on bit direction (working→failed or failed→working) instead of index ordering. (File: `fiabilipy/markov.py`)
- **Testing plan**:
  - Add unit tests verifying that for N=1 and N=2, transition directions match component rates and that availability computed by `Markovprocess.value()` matches `System.availability()` for simple systems. (Files: `fiabilipy/test_markov.py`, new tests)
- **Risks / edge cases**: Changing semantics is potentially breaking; provide backward-compatibility or a version gate.
- **Complexity**: M

### 2) Python 3 port + packaging modernization (Priority: P0)
- **Problem statement / user story**: As a maintainer, I need to install and test on modern Python without Python 2 dependencies or syntax issues. (Sources: `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipy-2.7/fiabilipy/voter.py`)
- **Proposed API**: No API change; same public classes.
- **Implementation sketch**:
  - Replace `xrange` → `range`, `collections.Iterable` → `collections.abc.Iterable`, import `reduce` from `functools`. (Files: `fiabilipy/system.py`, `fiabilipy/voter.py`, tests)
  - Add `pyproject.toml` / `setup.cfg` with explicit `install_requires` and extras for optional visualization (`graphviz`, `pygraphviz`, `matplotlib`). (Packaging files)
- **Testing plan**:
  - Add a CI matrix for Python 3.9+ and run `pytest` (or keep unittest) with numpy/scipy versions pinned. (New CI config)
- **Risks / edge cases**: Sympy/scipy version compatibility; performance regressions due to Sympy changes.
- **Complexity**: M

### 3) Declarative RBD construction helpers (Priority: P1)
- **Problem statement / user story**: As a user, I want to build series/parallel/voter structures without manual graph wiring. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Proposed API**:
  - `System.series(*components) -> System`
  - `System.parallel(*components) -> System`
  - `System.k_out_of_n(component, k, n) -> System` (or reuse `Voter`)
- **Implementation sketch**:
  - Add classmethods to `System` or helper functions in a new module `fiabilipy/rbd.py`. They should build a `System` with nodes wired between `'E'` and `'S'`. (Files: new `fiabilipy/rbd.py`, `fiabilipy/__init__.py` export)
- **Testing plan**:
  - Construct series and parallel systems and compare reliability to analytic formulas. (Files: new tests)
- **Risks / edge cases**: Component naming collisions and shared components; document expectations.
- **Complexity**: S/M

### 4) Reliability computation via minimal path/cut sets with pruning (Priority: P1)
- **Problem statement / user story**: As systems grow, inclusion–exclusion over all success paths becomes intractable. I need better scaling or approximate methods. (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Proposed API**:
  - `System.reliability(t, method='inclusion_exclusion'|'minimal_paths'|'monte_carlo')`
- **Implementation sketch**:
  - Add a minimal path set enumerator using networkx and prune with dominance rules; compute reliability using path/cut sets to reduce complexity. (File: `fiabilipy/system.py` or new module)
  - Optionally integrate a BDD-based solver for exact reliability on larger systems (longer bet).
- **Testing plan**:
  - Compare methods on small systems to ensure exact match, and validate convergence for approximate method. (New tests)
- **Risks / edge cases**: Complexity can still blow up; need explicit warnings or method switches.
- **Complexity**: M/L

### 5) Monte Carlo simulation for time-dependent metrics (Priority: P1)
- **Problem statement / user story**: I want to model non-exponential failure/repair or large systems where analytic solutions are too costly. (Sources: `fiabilipy-2.7/fiabilipy/component.py`, `fiabilipy-2.7/fiabilipy/system.py`)
- **Proposed API**:
  - `System.simulate(t, n=10000, rng=None)` returning estimated reliability/availability.
  - `Component(distribution=..., params=...)` (optional extension for non-exponential distributions).
- **Implementation sketch**:
  - Add a simulation module that samples component up/down trajectories and evaluates system availability via the graph at sampled times. (New module, e.g. `fiabilipy/sim.py`)
- **Testing plan**:
  - For exponential distributions, compare simulation estimates to analytic results with tolerance bounds. (New tests)
- **Risks / edge cases**: Randomness, performance, and variance; document statistical error.
- **Complexity**: M

### 6) Explicit dependency/optional extras + robust draw utilities (Priority: P2)
- **Problem statement / user story**: Graph drawing currently depends on graphviz layout and networkx internals with no error handling or optional dependency separation. (Sources: `fiabilipy-2.7/fiabilipy/system.py`, `fiabilipyg-2.8.1/fiabilipy/system.py`)
- **Proposed API**:
  - `System.draw(layout='graphviz', labels=True, ax=None)`
  - `Markovprocess.draw(layout='graphviz', labels=True, ax=None)`
- **Implementation sketch**:
  - Move visualization to a `viz.py` module, import optional deps lazily, and provide clearer error messages if graphviz/pygraphviz/matplotlib are missing. (New module + changes to `system.py`, `markov.py`)
- **Testing plan**:
  - Smoke tests that ensure functions run with and without optional deps (skip if missing). (New tests)
- **Risks / edge cases**: Graphviz availability is platform-dependent; add optional extras in packaging.
- **Complexity**: S/M

### 7) Structured serialization and reproducibility (Priority: P2)
- **Problem statement / user story**: I want to persist system definitions and reload them reliably (especially in teaching). (Source: `fiabilipy-2.7/fiabilipy/system.py`)
- **Proposed API**:
  - `System.to_dict()` / `System.from_dict(d)`
  - `Component.to_dict()` / `Component.from_dict(d)`
- **Implementation sketch**:
  - Serialize components with names + rates, system edges as adjacency lists; include compatibility layer for `Voter`.
- **Testing plan**:
  - Round-trip tests: build system → dict → restore → same reliability results. (New tests)
- **Risks / edge cases**: Shared components referenced in multiple locations; need consistent IDs.
- **Complexity**: M

## Quick wins vs longer bets
- **Quick wins**: P0 fixes for Markov state encoding, packaging modernization, and RBD helper constructors. (Files: `fiabilipy/markov.py`, packaging metadata, new helper module)
- **Longer bets**: Reliability computation scaling (BDD/minimal path methods) and Monte Carlo simulation framework. (Files: `fiabilipy/system.py`, new `sim.py`)

## Testing & tooling upgrades
- Migrate tests to `pytest` (optional) while keeping current unittest suites intact to minimize churn. (Sources: `fiabilipy-2.7/fiabilipy/test_system.py`, `fiabilipy-2.7/fiabilipy/test_markov.py`)
- Add coverage for `Component`, `Voter`, `faulttreeanalysis()`, and visualization modules. (Sources: `fiabilipy-2.7/fiabilipy/component.py`, `fiabilipy-2.7/fiabilipy/voter.py`, `fiabilipy-2.7/fiabilipy/system.py`)
- Add CI to run tests across supported Python versions and common dependency sets.

## Documentation / examples upgrades
- Expand README with 2–3 complete examples for RBD, voter, and Markov usage, including expected outputs. (Sources: `fiabilipy-2.7/README`, `fiabilipy-2.7/fiabilipy/*` docstrings)
- Add a dedicated “state encoding” section for Markov models with a clear bit convention and mapping examples. (Source: `fiabilipy-2.7/fiabilipy/markov.py`)
