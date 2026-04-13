# Notation Convention Guide

Authoritative reference for variable naming across all 20 optimization tutorials.

---

## Principle

Every symbol used in LaTeX must have an identical or clearly mapped Python
variable name. When a reader sees $\theta_2$ in an equation, the code must use
`theta2` (not `angle`, not `th`, not `x[0]`).

If you find a mismatch between a notebook's LaTeX and its Python, fix it. This
guide is the single source of truth.

---

## Mechanism Variables

| LaTeX | Python | Description | Units |
|-------|--------|-------------|-------|
| $r_1$ | `r1` | Ground link length | normalized (dimensionless) |
| $r_2$ | `r2` | Crank (input) link length | normalized |
| $r_3$ | `r3` | Coupler link length | normalized |
| $r_4$ | `r4` | Follower (output) link length | normalized |
| $\theta$ or $\theta_2$ | `theta` or `theta2` | Input crank angle | radians |
| $\phi$ or $\theta_4$ | `phi` or `theta4` | Follower angle (computed) | radians |
| $\theta_3$ | `theta3` | Coupler angle (computed) | radians |
| $P$ | `P` | Coupler point position | (x, y) tuple |
| $P_x, P_y$ | `Px`, `Py` | Coupler point coordinates | normalized |
| $P^{\text{target}}$ | `P_target` | Target precision point(s) | (N, 2) array |
| $P^{\text{actual}}$ | `P_actual` | Computed coupler point(s) | (N, 2) array |
| $p$ | `p_coupler` | Coupler point parameter (along BC) | fraction [0, 1] |
| $h$ | `h_coupler` | Coupler point offset (perp to BC) | normalized |
| $n_{\text{pts}}$ | `n_pts` | Number of precision points | integer (= 6) |

---

## Optimization Variables

| LaTeX | Python | Description |
|-------|--------|-------------|
| $f$ | `f_obj` | Objective function value |
| $\mathbf{x}$ | `x` | Design variable vector |
| $\nabla f$ | `grad_f` | Gradient of objective |
| $\mathbf{H}$ | `H` | Hessian matrix |
| $g_i(\mathbf{x})$ | `g[i]` or `g_i` | Inequality constraint ($g \le 0$ convention) |
| $h_j(\mathbf{x})$ | `h_eq[j]` or `h_j` | Equality constraint ($h = 0$) |
| $\lambda$ | `lam` | Lagrange multiplier (**not** `lambda` -- Python keyword) |
| $\mu$ | `mu` | Penalty parameter |
| $\alpha$ | `alpha` | Step size / learning rate |
| $\epsilon$ | `eps` | Tolerance |
| $\delta$ | `delta` | Finite difference step |

---

## Subscript and Superscript Convention

| Pattern | LaTeX | Python | Example |
|---------|-------|--------|---------|
| Subscript index | $x_i$ | `x[i]` or `x_i` | `r2` for $r_2$ |
| Superscript iteration | $x^{(k)}$ | `x_k` | `x_0` for initial guess |
| Optimal value | $x^*$ | `x_star` | `r2_star` for optimal crank length |
| Target/desired | $P^{\text{target}}$ | `P_target` | target precision points |
| Lower/upper bound | $x^L, x^U$ | `x_lb`, `x_ub` | bounds on design variables |

---

## Function Naming Convention

| Category | Pattern | Examples |
|----------|---------|----------|
| Objective functions | `f_` prefix | `f_obj`, `f_structural_error`, `f_transmission` |
| Inequality constraints | `g_` prefix | `g_grashof`, `g_assembly` |
| Equality constraints | `h_` prefix | `h_loop_closure`, `h_eq` |
| Kinematics utilities | descriptive snake_case | `forward_kinematics`, `structural_error`, `grashof_check` |
| Plotting helpers | descriptive snake_case | `draw_fourbar`, `plot_objective_contour` |

---

## Array Conventions

| Data | Shape | Notes |
|------|-------|-------|
| Design variable vector | `(n,)` 1D array | e.g. `x = np.array([r2, r3])` |
| Precision points | `(N, 2)` array | Each row is `(x, y)` |
| Crank angles | `(N,)` 1D array | Evenly spaced, in **radians** |
| Gradient | `(n,)` 1D array | Same shape as design vector |
| Hessian | `(n, n)` 2D array | Symmetric |

---

## Reserved Names (Do Not Reuse)

These Python keywords or common names must be avoided as variable names:

| Avoid | Use Instead | Reason |
|-------|-------------|--------|
| `lambda` | `lam` | Python keyword |
| `type` | `opt_type` | Python builtin |
| `input` | `theta_input` | Python builtin |
| `sum` | `total` or explicit `np.sum(...)` | Python builtin |
| `min` / `max` | `r_min` / `r_max` or `np.min(...)` | Python builtin |
| `id` | `point_id` | Python builtin |

---

*This document is a reference, not a tutorial. Keep it open while writing
notebooks. If a new symbol is introduced in a later tutorial, add it here first.*
