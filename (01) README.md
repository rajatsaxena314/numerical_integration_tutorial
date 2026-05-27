# Computational Physics: Orbital Mechanics & Numerical Integration

This suite of interactive Jupyter notebooks is designed to walk you through computational physics, numerical analysis, and classical mechanics. Instead of treating numerical integration algorithms as abstract math, this repository approaches them through a clear, pedagogical sequence: you will build intuition for *why* standard methods fail, *how* symplectic integrators fix those failures, and *what* these stable methods reveal when applied to real physics — from planetary orbits to molecular forces.

---

## Recommended Order

```
[1. Comparison] ──> [2. Stability] ──> [3. Lennard-Jones] ──> [4. Conservation] ──> [5. Kepler Verification]
```

Each notebook builds directly on the physical intuition and numerical tools established by the one before it. Following this sequence is strongly recommended.

---

## Step 1: `Comparison_of_integrators.ipynb`

**What it does:** Introduces the concept of a numerical integrator from the ground up and sets up a simple planetary simulation under Newtonian gravity. It then pits two methods directly against each other — the classical **Euler Method** and the **Leapfrog Integrator** — so you can immediately see the consequences of each design choice.

The Euler method updates position and velocity using only the current slope, assuming it stays constant over the entire timestep. Because real orbits are curved, this assumption introduces a small energy error at every step that compounds over time. Using a harmonic oscillator as a minimal example (where $F = -kx$ and $E = \frac{1}{2}v^2 + \frac{1}{2}x^2$), the notebook shows analytically that energy drifts upward after even a single Euler step — and that in a gravitational simulation, this causes a planet to unphysically spiral away from its star.

The Leapfrog integrator solves this through a staggered Kick–Drift–Kick structure: it advances velocity by a half-step, uses that to update position, recomputes acceleration at the new position, and then completes the velocity update. This time-symmetric design means errors cancel rather than accumulate.

**Why start here:** It establishes the central problem of the entire repository. Watching a planet drift off its orbit under the Euler method makes the need for a smarter integrator viscerally clear — and makes everything that follows feel earned rather than arbitrary.

---

## Step 2: `Stability_of_Orbitals.ipynb`

**What it does:** With the Leapfrog integrator now in hand, this notebook shifts focus to the physics itself. It derives the condition for a stable circular orbit — where gravitational attraction exactly balances centripetal requirements ($v = \sqrt{GM/r}$) — and then maps out what happens when you deviate from it.

By adjusting the initial tangential velocity relative to this threshold, the simulation produces three qualitatively distinct trajectories:

- **Stable circular orbit** ($v = v_\text{orbit}$): gravity and tangential motion remain in perfect balance.
- **Inward spiral** ($v < v_\text{orbit}$): gravity dominates and the particle falls toward the star.
- **Escape orbit** ($v > v_\text{orbit}$): kinetic energy exceeds gravitational binding and the particle breaks free.

Each case is visualised with trajectory plots coloured by orbital speed, making the physical boundaries between regimes immediately apparent.

**Why read it next:** It transforms the Leapfrog integrator from an abstract numerical tool into a genuine physical instrument. You get to ask real questions — *what happens if a planet forms slightly too slow, or too fast?* — and trust that the answers reflect true physics rather than numerical artefact.

---

## Step 3: `Lennard_Jones_Potential.ipynb`

**What it does:** Proves that the Leapfrog integrator is not just a gravitational trick — it is a general-purpose tool for any conservative force. This notebook swaps out the gravitational potential for the **Lennard-Jones (LJ) potential**, which models interactions between a pair of neutral atoms or molecules:

$$V(r) = 4\varepsilon \left[ \left(\frac{\sigma}{r}\right)^{12} - \left(\frac{\sigma}{r}\right)^{6} \right]$$

The two competing terms capture short-range Pauli repulsion (the $r^{-12}$ term) and long-range van der Waals attraction (the $r^{-6}$ term). The notebook derives the equilibrium distance $r_\text{min} = 2^{1/6}\,\sigma$, escape velocity $v_\text{escape} = \sqrt{2\varepsilon/m}$, and circular orbit speed $v_\text{orbit}$, then uses the same three-regime velocity sweep from Step 2 to simulate bound, spiralling, and escaping molecular trajectories.

**Why read it next:** It demonstrates the true payoff of learning a robust integrator. The same Leapfrog code that simulated planets around a star now models atoms interacting through quantum-mechanical forces — only the force law changes. This transfer of method across physical scales is one of the most important lessons in computational physics.

---

## Step 4: `Conservation_and_Convergence.ipynb`

**What it does:** Returns to the gravitational two-body problem and subjects both integrators to rigorous quantitative testing. It tracks **total mechanical energy** ($E = \frac{1}{2}mv^2 - \frac{GMm}{r}$) and **angular momentum** ($\vec{L} = \vec{r} \times m\vec{v}$) over thousands of timesteps, plotting how faithfully each integrator preserves these conserved quantities.

The results are stark. Under Euler, energy and angular momentum drift steadily — errors accumulate step by step, eventually destabilising the orbit. Under Leapfrog, both quantities remain nearly constant, with only small bounded oscillations that never grow.

The second half of the notebook introduces **convergence analysis**: how does orbital accuracy respond to changes in timestep size $\Delta t$? The Euler method requires very small timesteps for acceptable accuracy and still degrades over long runs. The Leapfrog method produces nearly identical orbits across a wide range of timestep values, confirming its structural robustness.

**Why read it next:** This is where intuition becomes evidence. After seeing the integrators behave differently in Steps 1–3, this notebook provides the mathematical proof — the conservation and convergence plots that explain *why* Leapfrog stays stable and *why* Euler cannot be trusted for long simulations.

---

## Step 5: `Verification_of_Keplers_Laws.ipynb`

**What it does:** The capstone of the series. Using the **Velocity Verlet algorithm** — a symplectic integrator closely related to Leapfrog — the notebook runs a high-precision planetary simulation and uses it to empirically verify all three of Kepler's Laws:

- **First Law** — The trajectory is confirmed to be an ellipse by computing $d_1 + d_2$ (the sum of distances from the planet to both foci) at every timestep. The standard deviation of this sum comes out near $10^{-15}$, confirming it is perfectly constant.

- **Second Law** — The area swept by the radius vector in each timestep is computed via the cross product $\frac{1}{2}\|\mathbf{r}_\text{curr} \times \mathbf{r}_\text{next}\|$. The relative variance across all steps is again near $10^{-15}$, confirming equal areas in equal times and the conservation of angular momentum.

- **Third Law** — The simulation computes the empirical ratio $T^2/a^3$ and compares it to Newton's theoretical prediction $4\pi^2/GM$. The relative error between the two is negligibly small, confirming the universal scaling law.

**Why read it last:** It closes the loop on everything that came before. The numerical stability you built from Step 1, the physical intuition you developed in Steps 2 and 3, and the conservation guarantees you proved in Step 4 all converge here — letting you use simulation as a genuine scientific instrument to verify laws that took Kepler and Newton decades to discover.
