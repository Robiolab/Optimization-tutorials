# Running Example: Four-Bar Linkage Path Synthesis

This document specifies the four-bar path synthesis problem that threads through
all 20 optimization tutorials. It serves as the single reference for problem setup,
notation, and tutorial-by-tutorial usage.

---

## Problem Statement

A planar four-bar linkage consists of four rigid links connected by revolute
joints: a ground link (frame), a crank (input), a coupler, and a follower
(output). A coupler point P, rigidly attached to the coupler link, traces a
curve as the crank rotates.

**Path synthesis goal:** Find linkage dimensions such that the coupler point
passes through (or near) a set of prescribed precision points. The optimizer
minimizes the *structural error* -- the sum of squared deviations between the
actual coupler path and the target points.

---

## Design Variables

For Tutorial 1 simplicity, only **two design variables** are free:

| Variable | Description          | Range         |
|----------|----------------------|---------------|
| r2       | Crank length         | [0.3, 1.5]   |
| r3       | Coupler length       | [0.5, 2.0]   |

All other parameters are fixed (see below). Two variables allow the entire
objective landscape to be visualized as a single 2D contour plot -- essential
for building geometric intuition before scaling up.

### Fixed Parameters

| Parameter | Value | Description                              |
|-----------|-------|------------------------------------------|
| r1        | 1.0   | Ground link length (normalized)          |
| r4        | 1.0   | Follower link length                     |
| p         | 0.5   | Coupler point offset along coupler link  |
| h         | 0.3   | Coupler point offset perpendicular to coupler |

### Gradual Complexity Growth

The 2-variable formulation is the starting point. Later tutorials expand it:

- **Tutorial 10** adds coupler point parameters (p, h) as design variables (4D).
- **Tutorial 12** adds a second objective (transmission angle quality) for
  multi-objective formulation.
- **Tutorial 19** explores discrete topology choices alongside continuous sizing.

---

## Precision Points

The coupler point must pass through these 6 target positions, corresponding to
6 equally spaced crank angles theta_i over one revolution:

| Point | theta_i (deg) | Px_target | Py_target |
|-------|---------------|-----------|-----------|
| 1     |   0           |  1.80     |  0.60     |
| 2     |  60           |  1.50     |  1.05     |
| 3     | 120           |  0.90     |  1.15     |
| 4     | 180           |  0.45     |  0.80     |
| 5     | 240           |  0.55     |  0.30     |
| 6     | 300           |  1.20     |  0.15     |

These points trace a roughly elliptical arc in the right half-plane. They are
achievable by multiple distinct linkage configurations -- a deliberate choice to
ensure multi-modality (see below).

---

## Objective Function (Structural Error)

For a given (r2, r3), the structural error is:

```
f(r2, r3) = sum_{i=1}^{6} || P_actual(theta_i; r2, r3) - P_target_i ||^2
```

where P_actual is computed via forward kinematics of the four-bar:

1. For each crank angle theta_i, solve the loop-closure equation for the
   follower angle phi_i.
2. Compute the coupler point position P(theta_i) from the known geometry.
3. Sum squared Euclidean distances to the target points.

If the loop-closure equation has no real solution for a given (r2, r3, theta_i),
the linkage cannot assemble at that configuration. Such cases receive a large
penalty value.

---

## Required Properties

### 1. Two Design Variables

**Why it matters:** Tutorial 1 needs a problem simple enough to visualize as a
contour plot. Two variables means the entire objective landscape fits in a
single 2D heatmap -- the reader can literally *see* the minimum, the ridges,
and the infeasible regions. This builds intuition before introducing gradient
descent in Tutorial 3.

### 2. Visible Multi-Modality

**Why it matters:** Tutorial 13 is the narrative turning point where gradient
methods are shown to fail. The objective landscape must have at least 2-3
distinct basins of attraction so that gradient descent from different starting
points demonstrably converges to different local minima.

**Why four-bar synthesis is naturally multi-modal:** Different linkage
proportions can produce similar coupler paths through fundamentally different
geometric configurations. A short-crank/long-coupler linkage and a
long-crank/short-coupler linkage can both approximate the same target path,
but through different kinematic mechanisms. Additionally, the loop-closure
equation admits two assembly modes (elbow-up vs. elbow-down), each producing
a different coupler curve for the same dimensions. These geometric symmetries
create multiple local minima in the structural error landscape.

### 3. Meaningful Constraints

**Why it matters:** Tutorial 11 must demonstrate that real mechanism design
involves hard constraints -- not just soft penalties. Two key constraints arise:

- **Grashof condition:** For a crank-rocker mechanism, the shortest link plus
  the longest link must be less than or equal to the sum of the other two:
  `min(r1,r2,r3,r4) + max(r1,r2,r3,r4) <= sum of other two`. With r1=r4=1.0,
  varying r2 and r3 means some regions of the design space violate Grashof and
  do not produce full crank rotation.

- **Assembly/branch ambiguity:** The loop-closure equation is quadratic in
  cos(phi), yielding two solutions (branches). The optimizer must select and
  maintain a consistent branch. Switching branches mid-path produces a
  discontinuous coupler curve.

Both constraints are active in the design space -- they are not trivially
satisfied for all (r2, r3), making them pedagogically useful for constrained
optimization tutorials.

### 4. Gradual Complexity Growth

**Why it matters:** The tutorial series must feel like climbing a staircase, not
hitting a wall. The four-bar problem scales naturally:

| Tutorial | Variables | Objectives | Constraints      |
|----------|-----------|------------|------------------|
| 1-4      | 2 (r2, r3)| 1 (f_obj)  | None (unconstrained intro) |
| 5-8      | 2         | 1          | Grashof (inequality) |
| 9-10     | 2-4       | 1          | Grashof + loop closure |
| 11       | 4         | 1          | Full constraint set |
| 12       | 4         | 2          | Full constraint set |
| 13-16    | 2         | 1          | Back to basics for global search |
| 17-20    | 4+        | 1-2        | Full formulation |

---

## Tutorial Usage Map

| Tutorial | Title                          | Four-Bar Usage                                                        |
|----------|--------------------------------|-----------------------------------------------------------------------|
| 1        | What Is Optimization?          | Introduce f(r2, r3); plot contour of structural error                 |
| 2        | Types of Optimization          | Classify the problem: continuous, nonlinear, (un)constrained          |
| 3        | Gradient Descent               | Run gradient descent on f(r2, r3); show convergence path on contour   |
| 4        | Numerical Gradients            | Compute df/dr2, df/dr3 via finite differences; compare step sizes     |
| 5        | Newton & Quasi-Newton          | Apply BFGS to f; compare convergence rate vs. steepest descent        |
| 6        | KKT Conditions                 | Write KKT system for f with Grashof constraint; interpret multipliers |
| 7        | SQP                            | scipy.optimize.minimize with SLSQP on constrained four-bar problem   |
| 8        | Penalty & Augmented Lagrangian | Penalty method on Grashof constraint; show exact penalty convergence  |
| 9        | Synthesis Objective            | Formal structural error definition; weighted vs. minimax formulation  |
| 10       | Parameterizing the Mechanism   | Add coupler point (p, h) as variables; now 4D optimization            |
| 11       | Loop Closure Constraints       | Formulate loop-closure as equality constraint; branch selection       |
| 12       | Multi-Objective Optimization   | Add transmission angle quality as second objective; Pareto front      |
| 13       | Why Local Methods Fail         | Show multiple local minima on same contour; different starts diverge  |
| 14       | Genetic Algorithms             | GA on f(r2, r3); population on contour plot; generation evolution     |
| 15       | Particle Swarm Optimization    | PSO swarm movement visualization on the structural error landscape    |
| 16       | Differential Evolution         | DE finds global optimum; compare eval count with GA and PSO           |
| 17       | Surrogate & Bayesian Opt.      | Expensive-to-evaluate four-bar; surrogate model of f                  |
| 18       | CasADi Auto-Differentiation    | Symbolic four-bar kinematics in CasADi; exact gradients vs. FD       |
| 19       | Topology Optimization          | Discrete choices (crank-rocker vs. rocker-crank) + continuous sizing  |
| 20       | Hybrid Strategies              | DE warm-start to SQP polish; best of both worlds on four-bar          |

---

## Notation Convention

These variable names will be used consistently across all tutorials:

| Symbol       | Meaning                                      |
|--------------|----------------------------------------------|
| r1           | Ground link length (fixed = 1.0)             |
| r2           | Crank length (design variable)               |
| r3           | Coupler length (design variable)             |
| r4           | Follower link length (fixed = 1.0)           |
| theta        | Input crank angle                            |
| phi          | Follower angle (computed from loop closure)   |
| p            | Coupler point offset along coupler link      |
| h            | Coupler point offset perpendicular to coupler|
| Px, Py       | Coupler point position (x, y components)     |
| P_target     | Target precision point                       |
| P_actual     | Actual coupler point position                |
| f_obj        | Objective function value (structural error)  |
| g_grashof    | Grashof constraint function                  |
| g_loop       | Loop-closure constraint                      |
| n_pts        | Number of precision points (= 6)             |

---

## Forward Kinematics Reference

Given crank angle theta, the follower angle phi is found from the loop-closure
equation:

```
r2*cos(theta) + r3*cos(alpha) = r1 + r4*cos(phi)
r2*sin(theta) + r3*sin(alpha) = r4*sin(phi)
```

where alpha is the coupler angle. Eliminating alpha yields a single equation
in phi that can be solved via the half-angle substitution t = tan(phi/2),
producing a quadratic in t. The two roots correspond to the two assembly modes
(branches).

The coupler point position is then:

```
Px = r2*cos(theta) + p*cos(alpha) - h*sin(alpha)
Py = r2*sin(theta) + p*sin(alpha) + h*cos(alpha)
```

where alpha = atan2(r4*sin(phi) - r2*sin(theta), r4*cos(phi) - r2*cos(theta) - r1) + ... (exact expression depends on convention; implementation in utils/mechanism.py in Phase 3).

---

*This document is the reference specification for the running example. All tutorials point back here for problem setup and notation.*
