# Optimization Tutorials

A hands-on optimization tutorial series for mechanical engineers, built around a four-bar linkage synthesis problem.

Fourteen Jupyter notebooks progress from "what is an optimization problem?" through gradient descent, constrained optimization, global search, and analytical gradients via the implicit function theorem. Every concept is grounded in a single running example: synthesizing a planar four-bar linkage to trace a prescribed coupler path.

The tutorials use the [`dms`](https://github.com/JonathanCamargo/dynamics_of_mechanical_systems) library for mechanism visualization, and a fast inline numpy implementation of four-bar forward kinematics for computation-heavy cells (contour grids, optimization loops). Each notebook is self-contained — no shared utility module to learn.

## Quick Start

1. Clone the repository:
   ```bash
   git clone https://github.com/your-username/optimization_tutorials.git
   cd optimization_tutorials
   ```

2. Install dependencies (Python 3.11+):
   ```bash
   pip install -r requirements.txt
   ```

3. Start learning:
   ```bash
   jupyter notebook notebooks/01_what_is_optimization.ipynb
   ```

## Learning Path

The tutorials are organized into four modules. Each tutorial lists its prerequisites so you can skip ahead if you already know the material.

### Module 1 — Foundations (Tutorials 1-4)

| # | Tutorial | Description | Prerequisites |
|---|----------|-------------|---------------|
| 1 | What Is an Optimization Problem? | Design variables, objectives, constraints, and feasible regions | None |
| 2 | Types of Optimization Problems | Linear vs. nonlinear, constrained vs. unconstrained, convex vs. non-convex | T01 |
| 3 | Gradient Descent from Scratch | Steepest descent, step size, convergence on the linkage landscape | T01, T02 |
| 4 | Numerical Gradients | Forward, central, and complex-step finite differences; accuracy analysis | T01, T03 |

### Module 2 — Constrained Methods (Tutorials 5-8)

| # | Tutorial | Description | Prerequisites |
|---|----------|-------------|---------------|
| 5 | Newton and Quasi-Newton Methods | Second-order methods, Hessian approximation, BFGS | T03, T04 |
| 6 | KKT Conditions | Optimality for constrained problems, Lagrange multipliers, the Grashof constraint | T05 |
| 7 | Sequential Quadratic Programming | SQP algorithm, quadratic subproblems, SLSQP in SciPy | T05, T06 |
| 8 | Penalty and Augmented Lagrangian Methods | Quadratic penalty, augmented Lagrangian, parameter tuning | T06, T07 |

### Module 3 — Global Methods (Tutorials 9-12)

| # | Tutorial | Description | Prerequisites |
|---|----------|-------------|---------------|
| 9  | Why Local Methods Fail | Multi-modality, basin structure, motivation for global search | T03, T05 |
| 10 | Genetic Algorithms | Tournament selection, BLX-α crossover, Gaussian mutation, elitism | T09 |
| 11 | Particle Swarm Optimization | Position/velocity updates, inertia, cognitive and social terms | T10 |
| 12 | Differential Evolution | DE/rand/1/bin from scratch and `scipy.optimize.differential_evolution` | T11 |

### Module 4 — Advanced Methods (Tutorials 13-14)

| # | Tutorial | Description | Prerequisites |
|---|----------|-------------|---------------|
| 13 | Analytical Gradients | Implicit function theorem on the loop closure equation; speedup over finite differences | T04, T05 |
| 14 | Hybrid Strategies (Capstone) | DE for global search → BFGS with analytical gradients for polish | T12, T13 |

## The Running Example

Every tutorial uses the same problem: synthesizing a planar **four-bar linkage** so that a coupler point traces a prescribed path.

- **Design variables:** Two free link lengths, l2 (coupler) and l3 (follower), with l0 (ground) and l1 (crank) fixed at 1.0.
- **Objective:** Minimize the curve-comparison error between the coupler point trajectory and six target precision points along a horizontal line.
- **Constraints:** The Grashof condition (shortest + longest ≤ sum of the other two) ensures the crank can fully rotate.
- **Landscape:** The objective is multi-modal with multiple local minima, making it a natural testbed for both local and global optimization methods.

See `docs/four-bar-example.md` for the problem specification and `docs/notation-guide.md` for variable naming conventions.

## Project Structure

```
optimization_tutorials/
  notebooks/           # 14 tutorial notebooks (01-14)
  data/                # Reference data (precision points)
  docs/                # Notation guide, four-bar example specification
  dms_PR.md            # Tracked enhancements to the dms library
  requirements.txt     # Python dependencies
```

## Prerequisites

- **Python 3.11** or newer
- **Core libraries:** NumPy, SciPy, Matplotlib, SymPy
- **dms:** [`dynamics_of_mechanical_systems`](https://github.com/JonathanCamargo/dynamics_of_mechanical_systems) for mechanism visualization (installed from git via `requirements.txt`)
- **Jupyter Notebook** or **JupyterLab** for running the tutorials

All dependencies are listed in `requirements.txt`.

## License

MIT
