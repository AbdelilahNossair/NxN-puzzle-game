# N × N Sliding Puzzle Search

A Python extension of sliding-puzzle search to square boards of configurable size. It includes state transitions, DFS/BFS/UCS implementations, A* experiments, and four size-aware heuristics. The bundled scripts focus on the 4 × 4 board.

## Representation and goal

`NPuzzleState(numbers, N)` accepts a flat row-major sequence containing `0` through `N*N-1`; zero represents the blank. The goal starts with the blank at the upper left:

```text
 0  1  2  3
 4  5  6  7
 8  9 10 11
12 13 14 15
```

Legal actions move the blank one cell and cost one. `shuffle_puzzle` applies legal moves from a solved state, preserving reachability.

## Setup

```bash
git clone https://github.com/AbdelilahNossair/NxN-puzzle-game.git
cd NxN-puzzle-game
python3 -m venv .venv
source .venv/bin/activate
python -m pip install pandas
```

Python 3 is required. pandas is used by the experiment runner; core search uses the standard library. On Windows use `.venv\Scripts\activate`.

## Small example

Run from the repository root:

```python
import npuzzle
import search

n = 3
start = npuzzle.NPuzzleState([1, 0, 2, 3, 4, 5, 6, 7, 8], n)
problem = npuzzle.NPuzzleSearchProblem(start)
path, expanded, depth, fringe = search.aStarSearch(problem, search.h3, n)

state = start
for move in path:
    state = state.result(move)
assert state.isGoal()
print(path, expanded, depth, fringe)
```

A* returns **four values**. The bundled `npuzzle.py` main block assigns that tuple to one variable and prints `len(path)`, so its reported move count is misleading; use the explicit unpacking above.

## Search and heuristics

| Function | Purpose |
| --- | --- |
| `depthFirstSearch` | Stack-based graph traversal |
| `breadthFirstSearch` | Queue-based traversal |
| `uniformCostSearch` | Priority by accumulated cost |
| `aStarSearch(problem, heuristic, n)` | Heuristic priority search |
| `h1` | Misplaced nonblank tiles |
| `h2` | Sum of Euclidean tile distances |
| `h3` | Sum of Manhattan tile distances |
| `h4` | Count of incorrect rows plus incorrect columns |

A* precomputes goal positions. Heuristics receive the state, goal matrix, and position lookup internally.

## CSV experiments

`automate.py` expects a local, headerless `scenarios.csv` containing **16 integers per row** for its hard-coded N=4 experiments. Neither that data file nor a scenario-generation script is included.

Prepare solvable boards by legal shuffling, write each flattened board as a CSV row, then run:

```bash
python automate.py
```

The default entry point writes `detailed_results_task2.csv` and `averages_results_task2.csv` for h1–h4. Additional search comparisons exist in `task3`, but their invocation is commented out. They can be expensive on 4 × 4 boards.

## Architecture

`npuzzle.py` defines the board, search-problem adapter, shuffle helper, and demo. `search.py` contains algorithms and heuristics; `util.py` supplies data structures; `automate.py` handles CSV input and reporting.

## Limitations

The A* implementation mixes heuristic values into its running-cost calculation and then adds a heuristic again when pushing successors. Treat it as an experimental implementation: optimality and benchmark comparisons require correction and regression checks. The scripts also lack board validation, parity checks for user-provided configurations, resource limits, and reproducible sampling controls. Node/fringe counters are not consistent across all algorithms.

This project is related to [8-puzzle-game](https://github.com/AbdelilahNossair/8-puzzle-game). Consolidating both into one maintained solver would reduce duplicated search code.

## Attribution

Preserve the UC Berkeley educational-use notices in inherited search utilities and credit [UC Berkeley AI](http://ai.berkeley.edu). No standalone license file is included.
