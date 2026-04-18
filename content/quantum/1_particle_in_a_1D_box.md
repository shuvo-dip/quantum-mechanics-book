---
numbering:
  title:
    offset: 0

kernelspec:
  name: python3
  display_name: 'Python 3'
---

(ch-pib)=
# A Particle in a 1D Box

Quantum mechanics governs the behaviour of matter at the smallest scales — atoms, electrons, and molecules. Unlike classical mechanics, where a particle can sit at rest with zero energy, quantum mechanics reveals a world of discrete energy levels, wave-like behaviour, and irreducible uncertainty. In this chapter we build the foundational tools to understand quantum mechanics and apply them to the simplest possible confined system: a particle trapped in a one-dimensional box.

This system is not merely a toy problem. It provides direct insight into the electronic structure of conjugated molecules, the physics of quantum dots, and the statistical mechanics of ideal gases. More importantly, it introduces you to the central ideas — wavefunctions, energy quantisation, normalisation, and expectation values — that appear in every quantum mechanical problem you will ever encounter.

This chapter follows a careful, step-by-step approach:

1. We introduce quantum mechanical **operators** and **eigenvalue equations**.
2. We state the meaning of the **wavefunction** and the **Schrödinger equation**.
3. We solve the **free particle** and the **particle in an infinite 1D box**.
4. We explore the **properties** of the solutions: nodes, zero-point energy, probability densities, and expectation values.
5. We extend the problem to **2D and 3D boxes**, and meet the concept of **degeneracy**.
6. We connect the model to **real physical systems**: conjugated molecules and quantum dots.
7. We implement **numerical simulations** in Python to visualise wavefunctions and probability densities.

(ch_pib_s_operators)=
## Operators and Eigenvalue Equations

Before writing down the Schrödinger equation, we need the language it is written in: the language of **operators**.

(ch_pib_ss_operators)=
### Linear Operators

An **operator** $\hat{A}$ is an instruction to *do something* to the function on its right:

$$\hat{A} f(x) = g(x)$$

We are exclusively interested in **linear operators**, which satisfy:

$$\hat{A}\bigl[af(x) + bg(x)\bigr] = a\hat{A}f(x) + b\hat{A}g(x)$$

for any constants $a$ and $b$.

```{example} Linear operators
Some familiar linear operators:

| Operator $\hat{A}$ | Action | Example |
|---|---|---|
| $\frac{d}{dx}$ | differentiate w.r.t. $x$ | $\frac{d}{dx}e^{ax} = ae^{ax}$ |
| $\frac{d^2}{dx^2}$ | differentiate twice | $\frac{d^2}{dx^2}\sin(bx) = -b^2\sin(bx)$ |
| $x\frac{d}{dx}$ | multiply by $x$, then differentiate | $x\frac{d}{dx}(ax^n) = nax^n$ |

The square-root operator $\sqrt{\cdot}$ is *not* linear, since $\sqrt{af+bg} \neq a\sqrt{f}+b\sqrt{g}$ in general.
```

(ch_pib_ss_eigenvalue)=
### Eigenvalue Equations

A particularly important situation arises when an operator acts on a function and returns the *same function*, multiplied by a constant:

$$\hat{A} f_n(x) = a_n f_n(x)$$

- $a_n$ is called the **eigenvalue** of $\hat{A}$ belonging to $f_n$.
- $f_n(x)$ is the corresponding **eigenfunction**.

```{important}
The Schrödinger equation is an eigenvalue equation. Finding its eigenfunctions and eigenvalues is the central task of quantum mechanics.
```

```{example} Eigenvalue equations
Check the following:

1. $\frac{d}{dx}e^{ax} = a\,e^{ax}$, so $e^{ax}$ is an eigenfunction of $\frac{d}{dx}$ with eigenvalue $a$.
2. $\frac{d^2}{dx^2}\sin(bx) = -b^2\sin(bx)$, so $\sin(bx)$ is an eigenfunction of $\frac{d^2}{dx^2}$ with eigenvalue $-b^2$.
3. $\frac{d^2}{dx^2}\cos(bx) = -b^2\cos(bx)$, so $\cos(bx)$ is also an eigenfunction with the same eigenvalue $-b^2$.
```

+++ { "page-break": true }
+++

(ch_pib_s_postulates)=
## The Postulates of Quantum Mechanics

Quantum mechanics rests on a set of postulates — foundational rules that cannot be derived, but whose validity is confirmed by the extraordinary accuracy of their predictions.

(ch_pib_ss_wavefunction)=
### The Wavefunction

**Postulate 1.** The complete information about a quantum system is contained in its **wavefunction** $\psi(x,t)$. The wavefunction is a complex-valued function of position and time.

The wavefunction itself cannot be observed directly. What is physically meaningful is the **probability density**:

$$P(x,t) = \psi^*(x,t)\,\psi(x,t) = |\psi(x,t)|^2$$

The probability of finding the particle between $x$ and $x+dx$ at time $t$ is:

$$dP = |\psi(x,t)|^2\, dx$$

Because the particle must be *somewhere*, the wavefunction must be **normalised**:

$$\int_{-\infty}^{+\infty} |\psi(x,t)|^2\, dx = 1$$

```{note}
The wavefunction $\psi$ is called a **probability amplitude**. It can be positive or negative (and complex!), and it can exhibit interference — a purely quantum mechanical effect with no classical analogue.
```

(ch_pib_ss_qm_operators)=
### Quantum Mechanical Operators

**Postulate 2.** Every observable physical quantity has a corresponding linear operator. The most important ones in one dimension are:

| Observable | Operator | Symbol |
|---|---|---|
| Position | multiply by $x$ | $\hat{x} = x$ |
| Momentum | $-i\hbar \dfrac{\partial}{\partial x}$ | $\hat{p}_x$ |
| Kinetic energy | $-\dfrac{\hbar^2}{2m}\dfrac{\partial^2}{\partial x^2}$ | $\hat{T}$ |
| Potential energy | multiply by $V(x)$ | $\hat{V}(x)$ |
| Total energy (Hamiltonian) | $\hat{T} + \hat{V}$ | $\hat{H}$ |

The Hamiltonian operator is therefore:

$$\hat{H} = -\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x^2} + V(x)$$

```{note} Why $\hat{p} = -i\hbar\frac{\partial}{\partial x}$?
At first sight the form of the momentum operator seems surprising. Its justification comes from the de Broglie relation $p = \hbar k$ and the fact that $-i\hbar \frac{\partial}{\partial x} e^{ikx} = \hbar k\, e^{ikx}$. The plane wave $e^{ikx}$ is an eigenfunction of $\hat{p}$ with eigenvalue $\hbar k = p$. The "truthiness" of this choice is ultimately confirmed by experiment.
```

(ch_pib_ss_commutator)=
### The Commutator and the Uncertainty Principle

Two operators $\hat{A}$ and $\hat{B}$ are said to **commute** if $\hat{A}\hat{B} = \hat{B}\hat{A}$, or equivalently if their **commutator** vanishes:

$$[\hat{A}, \hat{B}] \equiv \hat{A}\hat{B} - \hat{B}\hat{A} = 0$$

The position and momentum operators do **not** commute. We can show this by applying the commutator $[\hat{x}, \hat{p}]$ to an arbitrary test function $f(x)$:

$$\hat{x}\hat{p}\,f(x) = x\left(-i\hbar\frac{df}{dx}\right) = -i\hbar x\frac{df}{dx}$$

$$\hat{p}\hat{x}\,f(x) = -i\hbar\frac{d}{dx}(xf) = -i\hbar\left(f + x\frac{df}{dx}\right)$$

Subtracting:

$$[\hat{x},\hat{p}]\,f(x) = \hat{x}\hat{p}\,f - \hat{p}\hat{x}\,f = -i\hbar x\frac{df}{dx} - \left(-i\hbar f - i\hbar x\frac{df}{dx}\right) = i\hbar f(x)$$

Since $f(x)$ was arbitrary, we have:

$$\boxed{[\hat{x},\hat{p}] = i\hbar}$$

```{important}
This non-zero commutator is the fundamental mathematical origin of the **Heisenberg uncertainty principle**:

$$\Delta x \,\Delta p \geq \frac{\hbar}{2}$$

It is impossible to simultaneously specify the position and momentum of a quantum particle with arbitrary precision. This is not a limitation of our measuring apparatus — it is built into the structure of the theory.
```

(ch_pib_ss_expectation)=
### Expectation Values

**Postulate 4.** The **expectation value** (average value) of an observable $\hat{A}$ when the system is in state $\psi$ is:

$$\langle A \rangle = \frac{\int_{-\infty}^{+\infty} \psi^*(x)\,\hat{A}\,\psi(x)\,dx}{\int_{-\infty}^{+\infty} \psi^*(x)\,\psi(x)\,dx}$$

If $\psi$ is already normalised to 1, the denominator equals 1 and:

$$\langle A \rangle = \int_{-\infty}^{+\infty} \psi^*(x)\,\hat{A}\,\psi(x)\,dx$$

(ch_pib_ss_schrodinger)=
### The Schrödinger Equation

**Postulate 5.** The wavefunction evolves in time according to the **time-dependent Schrödinger equation**:

$$i\hbar\frac{\partial\Psi(x,t)}{\partial t} = \hat{H}\,\Psi(x,t) = \left[-\frac{\hbar^2}{2m}\frac{\partial^2}{\partial x^2} + V(x)\right]\Psi(x,t)$$

For systems with a **time-independent** potential $V(x)$, we can separate variables. Writing $\Psi(x,t) = \psi(x)\,\phi(t)$ and substituting:

$$i\hbar\,\psi(x)\frac{d\phi}{dt} = \phi(t)\,\hat{H}\,\psi(x)$$

Dividing both sides by $\psi(x)\phi(t)$, the left side depends only on $t$ and the right side only on $x$; both must equal the same constant $E$ (the energy):

$$\frac{d\phi}{dt} = -\frac{iE}{\hbar}\phi(t) \quad \Rightarrow \quad \phi(t) = e^{-iEt/\hbar}$$

$$\hat{H}\,\psi(x) = E\,\psi(x) \quad \longleftarrow \quad \textbf{Time-Independent Schrödinger Equation (TISE)}$$

The TISE is an eigenvalue equation for $\hat{H}$. Its solutions $\psi_n(x)$ are the **stationary states** (energy eigenstates), each associated with a definite energy eigenvalue $E_n$.

```{important} Complete solution
The full time-dependent stationary states are:

$$\Psi_n(x,t) = \psi_n(x)\,e^{-iE_n t/\hbar}$$

The general solution is a **linear superposition**:

$$\Phi(x,t) = \sum_n c_n\,\psi_n(x)\,e^{-iE_n t/\hbar}$$
```

+++ { "page-break": true }
+++

(ch_pib_s_roadmap)=
## Roadmap for Solving Quantum Problems

Every problem in quantum mechanics follows the same systematic procedure. We strongly recommend applying it every time.

::::{tab-set}

:::{tab-item} Step 1: Write Hamiltonian
Identify the potential $V(x)$ for the system and write down the Hamiltonian:
$$\hat{H} = -\frac{\hbar^2}{2m}\frac{d^2}{dx^2} + V(x)$$
:::

:::{tab-item} Step 2: Write the TISE
Write $\hat{H}\psi = E\psi$ explicitly and rearrange into a standard differential equation form.
:::

:::{tab-item} Step 3: Find general solutions
Identify the form of the general solution to the differential equation (usually sines, cosines, or exponentials).
:::

:::{tab-item} Step 4: Apply boundary conditions
Use physical requirements (continuity, normalisation, behaviour at $\pm\infty$) to constrain the solution. This step produces **quantisation**.
:::

:::{tab-item} Step 5: Normalise
Find the normalisation constant $A$ such that $\int |\psi_n|^2 dx = 1$.
:::

:::{tab-item} Step 6: Find Enery and Wave Fn
Write out the complete set of energy eigenvalues and eigenfunctions.
:::

::::

We will now apply this roadmap to two fundamental systems.

+++ { "page-break": true }
+++

(ch_pib_s_freeparticle)=
## The Free Particle

The free particle is the simplest quantum mechanical problem: a particle moving in one dimension with no forces acting on it.

**Setup:** $V(x) = 0$ everywhere.

**Step 1:** The Hamiltonian is:
$$\hat{H} = -\frac{\hbar^2}{2m}\frac{d^2}{dx^2}$$

**Step 2:** The TISE becomes:
$$-\frac{\hbar^2}{2m}\frac{d^2\psi}{dx^2} = E\psi \quad \Rightarrow \quad \frac{d^2\psi}{dx^2} = -\frac{2mE}{\hbar^2}\psi$$

**Step 3:** Define the **wavevector** $k$ by:
$$k^2 \equiv \frac{2mE}{\hbar^2} \quad (E > 0)$$

The general solution is:
$$\psi_k(x) = Ae^{ikx} + Be^{-ikx}$$

These are travelling plane waves. Using the complex exponential form, each solution is $\psi_k(x) = Ae^{ikx}$.

**Energies:** Solving for $E$:

$$\boxed{E_k = \frac{\hbar^2 k^2}{2m}}$$

Since $k$ can take any real value, the energy of a free particle is **continuous** — there is no quantisation.

```{note} Connection to de Broglie
The free particle wavefunction $e^{ikx}$ is an eigenfunction of the momentum operator:

$$\hat{p}\,e^{ikx} = -i\hbar\frac{d}{dx}e^{ikx} = \hbar k\, e^{ikx}$$

So a particle described by $e^{ikx}$ has definite momentum $p = \hbar k$. This is the quantum mechanical statement of the de Broglie relation $\lambda = h/p$.

However, if the momentum is perfectly definite, then by the uncertainty principle $\Delta x \to \infty$: the particle is completely delocalised in space. Indeed, $|\psi|^2 = |A|^2$ is uniform everywhere — the particle has equal probability of being found anywhere.
```

```{danger}
The free particle wavefunction $\psi = Ae^{ikx}$ is **not normalisable** over $(-\infty, +\infty)$, since $\int_{-\infty}^{+\infty}|A|^2\,dx = \infty$. Strictly speaking, it is not a physical state. This problem is resolved by forming **wave packets** — superpositions of plane waves with a spread in $k$ — which are localised and normalisable.
```

+++ { "page-break": true }
+++

(ch_pib_s_ibox)=
## Particle in an Infinite 1D Box

We now turn to the central model of this chapter: a particle confined to a one-dimensional box of length $a$ with infinitely hard walls. This is often called the **infinite square well** or **particle-in-a-box (PIB)**.

(ch_pib_ss_setup)=
### Setup and Boundary Conditions

The potential is:

$$V(x) = \begin{cases} 0 & 0 \leq x \leq a \\ \infty & x < 0 \text{ or } x > a \end{cases}$$


```{figure} ./images/QM_teaching_1.gif
:width: 99%
:label: fig_well
:alt: 
A schematic representation highlighting the differences between classical and quantum descriptions of a particle in an infinite potential well.
*[Visualization by Subhadip Biswas. Generated using [Manim](https://www.manim.community/).]*
```

**Physical interpretation:** The infinite walls prevent the particle from ever being outside the box. Inside the box, the particle moves freely.

**Consequences for $\psi$:**

- Outside the box, $\psi(x) = 0$ (a particle cannot exist where $V = \infty$, or else $\int \psi^* V \psi\, dx = \infty$, which is unphysical).
- The wavefunction must be **continuous** everywhere, so:

$$\boxed{\psi(0) = 0 \quad \text{and} \quad \psi(a) = 0}$$

These are the **boundary conditions**.

(ch_pib_ss_solving)=
### Solving the TISE

**Step 1:** Inside the box ($0 \leq x \leq a$), $V = 0$, so the Hamiltonian is:
$$\hat{H} = -\frac{\hbar^2}{2m}\frac{d^2}{dx^2}$$

**Step 2:** The TISE is:
$$-\frac{\hbar^2}{2m}\frac{d^2\psi}{dx^2} = E\psi \quad \Rightarrow \quad \frac{d^2\psi}{dx^2} = -k^2\psi, \quad k \equiv \sqrt{\frac{2mE}{\hbar^2}}$$

**Step 3:** The general solution is:
$$\psi(x) = A\sin(kx) + B\cos(kx)$$

**Step 4:** Apply boundary conditions.

*Left boundary:* $\psi(0) = 0$:
$$A\sin(0) + B\cos(0) = B = 0 \quad \Rightarrow \quad B = 0$$

*Right boundary:* $\psi(a) = 0$:
$$A\sin(ka) = 0$$

Since $A = 0$ gives the trivial (empty) solution, we require:
$$\sin(ka) = 0 \quad \Rightarrow \quad ka = n\pi, \quad n = 1, 2, 3, \ldots$$

```{important} Quantisation
The boundary condition forces $k$ to take only discrete values:

$$k_n = \frac{n\pi}{a}, \quad n = 1, 2, 3, \ldots$$

This is the origin of **quantisation**: not all energies are allowed, only specific discrete values. The integer $n$ is the **quantum number**.

Note: $n = 0$ is excluded because it gives $\psi = 0$ everywhere (no particle); negative $n$ gives the same wavefunctions up to a sign, which represents the same physical state.
```

The unnormalised eigenfunctions are therefore:
$$\psi_n(x) = A\sin\!\left(\frac{n\pi x}{a}\right), \quad n = 1, 2, 3, \ldots$$

(ch_pib_ss_normalisation)=
### Normalisation

**Step 5:** We determine $A$ from the normalisation condition $\int_0^a |\psi_n|^2\,dx = 1$:

$$A^2 \int_0^a \sin^2\!\left(\frac{n\pi x}{a}\right) dx = 1$$

Using the identity $\sin^2\theta = \frac{1}{2}(1 - \cos 2\theta)$:

$$A^2 \int_0^a \frac{1}{2}\left[1 - \cos\!\left(\frac{2n\pi x}{a}\right)\right] dx = A^2 \cdot \frac{a}{2} = 1$$

Therefore $A = \sqrt{2/a}$, and the **normalised eigenfunctions** are:

$$\boxed{\psi_n(x) = \sqrt{\frac{2}{a}}\sin\!\left(\frac{n\pi x}{a}\right), \quad n = 1, 2, 3, \ldots}$$

```{figure} ./images/QM_teaching_2.gif
:width: 99%
:label: fig_stationary
:alt: 
Schematic of an infinite potential well displaying the stationary wavefunction $\psi_n(x)$ and the associated probability density $|\psi_n(x)|^2$.
*[Visualization by Subhadip Biswas. Generated using [Manim](https://www.manim.community/).]*

```

(ch_pib_ss_energies)=
### Energy Eigenvalues

**Step 6:** We find the energy eigenvalues by substituting $k_n = n\pi/a$ into $E = \hbar^2 k^2 / 2m$:

$$E_n = \frac{\hbar^2 k_n^2}{2m} = \frac{\hbar^2}{2m}\left(\frac{n\pi}{a}\right)^2 = \frac{n^2\pi^2\hbar^2}{2ma^2} = \frac{n^2 h^2}{8ma^2}$$

where we used $\hbar = h/(2\pi)$. Defining $E_1 \equiv h^2/(8ma^2)$ as the **ground-state energy**:

$$\boxed{E_n = n^2 E_1, \quad E_1 = \frac{h^2}{8ma^2}}$$

The energies scale as $n^2$: the second level has four times the energy of the first, the third has nine times, and so on. The energy levels are **not equally spaced**.

+++ { "page-break": true }
+++


(ch_pib_s_properties)=
## Properties of the Solutions

(ch_pib_ss_nodes)=
### Nodes and Shape

The $n$th eigenfunction $\psi_n(x)$ has exactly $n-1$ **interior nodes** (zeros) located at equally spaced positions $x = ja/n$ for $j = 1, \ldots, n-1$.

| $n$ | Nodes | Energy |
|---|---|---|
| 1 | 0 | $E_1$ |
| 2 | 1 | $4E_1$ |
| 3 | 2 | $9E_1$ |
| 4 | 3 | $16E_1$ |

Higher energy states oscillate more rapidly and have more nodes. This pattern is universal in quantum mechanics: **higher energy always corresponds to more nodes**.

(ch_pib_ss_zpe)=
### Zero-Point Energy

A striking result: the lowest allowed energy is $E_1 = h^2/(8ma^2) > 0$. A confined quantum particle **cannot be at rest**. This irreducible minimum energy is called the **zero-point energy**.

Classically, a particle in a box could have zero kinetic energy — it simply sits still. Quantum mechanically, this is forbidden by the uncertainty principle: if the particle were at rest, $\Delta p = 0$, which would require $\Delta x \to \infty$, contradicting the confinement to a box of length $a$.

```{important}
Zero-point energy is a universal feature of any **confined** quantum system. It has measurable consequences: it is responsible for the stability of atoms (electrons cannot spiral into the nucleus), the rigidity of solids at absolute zero, and the Casimir effect between conducting plates.
```

(ch_pib_ss_scaling)=
### Scaling with Box Size

Since $E_n \propto 1/a^2$, as the box gets larger the energy levels drop rapidly and cluster more closely together. In the limit $a \to \infty$ (free particle), the discrete spectrum becomes continuous — we recover the classical result that any energy is allowed.

This behaviour is completely general: **confinement produces quantisation; releasing confinement destroys it**.

(ch_pib_ss_ortho)=
### Orthonormality

The eigenfunctions form an **orthonormal** set:

$$\int_0^a \psi_m^*(x)\,\psi_n(x)\,dx = \delta_{mn} = \begin{cases} 1 & m = n \\ 0 & m \neq n \end{cases}$$

where $\delta_{mn}$ is the Kronecker delta. This means:

- Each eigenfunction is **normalised** (integral = 1 for $m=n$).
- Different eigenfunctions are **orthogonal** (integral = 0 for $m \neq n$).

This is mathematically analogous to the dot product of perpendicular unit vectors in Euclidean space. The eigenfunctions form a complete basis: **any** physically admissible wavefunction can be expanded as:

$$\Phi(x) = \sum_{n=1}^{\infty} c_n\,\psi_n(x)$$

+++ { "page-break": true }
+++

(ch_pib_s_expectation)=
## Expectation Values for the Particle in a Box

With the eigenfunctions in hand, we can calculate the expectation values of all measurable quantities.

(ch_pib_ss_exp_x)=
### Average Position $\langle x \rangle$

$$\langle x \rangle_n = \int_0^a \psi_n^*(x)\,\hat{x}\,\psi_n(x)\,dx = \frac{2}{a}\int_0^a x\sin^2\!\left(\frac{n\pi x}{a}\right)dx$$

Using the integral $\displaystyle\int x\sin^2(ux)\,dx = \frac{x^2}{4} - \frac{x\sin(2ux)}{4u} - \frac{\cos(2ux)}{8u^2}$:

$$\boxed{\langle x \rangle_n = \frac{a}{2}}$$

The average position is always the **centre of the box**, regardless of $n$. This makes physical sense: the potential is symmetric about $x = a/2$, so the probability distribution must be symmetric about the centre.

(ch_pib_ss_exp_x2)=
### Uncertainty in Position $\Delta x$

$$\langle x^2 \rangle_n = \frac{2}{a}\int_0^a x^2\sin^2\!\left(\frac{n\pi x}{a}\right)dx = \frac{a^2}{3} - \frac{a^2}{2n^2\pi^2}$$

The variance (spread squared) is:

$$(\Delta x)^2 = \langle x^2\rangle - \langle x\rangle^2 = \frac{a^2}{12} - \frac{a^2}{2n^2\pi^2}$$

$$\boxed{\Delta x = a\sqrt{\frac{1}{12} - \frac{1}{2n^2\pi^2}}}$$

As $n$ increases, $\Delta x \to a/\sqrt{12}$: the particle's position uncertainty approaches the classical value (a uniform distribution over the box).

(ch_pib_ss_exp_p)=
### Average Momentum and Uncertainty $\Delta p$

$$\langle p \rangle_n = \frac{2}{a}\int_0^a \sin\!\left(\frac{n\pi x}{a}\right)\left(-i\hbar\frac{d}{dx}\right)\sin\!\left(\frac{n\pi x}{a}\right)dx = 0$$

The average momentum is zero. The particle has equal probability of moving to the right ($+\hbar k_n$) and to the left ($-\hbar k_n$) — it is a standing wave.

$$\langle p^2 \rangle_n = \frac{n^2\hbar^2\pi^2}{a^2}$$

$$\boxed{\Delta p_n = \frac{n\hbar\pi}{a}}$$

(ch_pib_ss_heisenberg)=
### Heisenberg Uncertainty Check

Combining the results above:

$$\Delta x\,\Delta p = \frac{\hbar}{2}\sqrt{\frac{n^2\pi^2}{3} - 2}$$

For the ground state ($n=1$): $\Delta x\,\Delta p = \frac{\hbar}{2}\sqrt{\frac{\pi^2}{3}-2} \approx 0.568\,\frac{\hbar}{2} > \frac{\hbar}{2}$ ✓

For large $n$: $\Delta x\,\Delta p \sim \frac{n\hbar\pi}{2\sqrt{3}} \to \infty$

```{important}
The **ground state** ($n = 1$) gives the **minimum uncertainty** product among all PIB states, and it satisfies $\Delta x\,\Delta p > \hbar/2$, consistent with the Heisenberg principle. No state of the PIB saturates the Heisenberg bound exactly (that would require a Gaussian wavefunction, which is not possible in a finite box).
```

+++ { "page-break": true }
+++

(ch_pib_s_numerical)=
## Numerical Simulation: Visualising the Particle in a Box

A great way to develop intuition for the PIB is to plot the wavefunctions and probability densities in Python. We can also compute expectation values numerically and verify our analytical results.

````{example} Plotting wavefunctions and probability densities

The code below plots $\psi_n(x)$ and $|\psi_n(x)|^2$ for the first several quantum numbers, with the energy levels shown as horizontal offsets.

```{code-cell} Python
:tag: hide-input

import numpy as np
import matplotlib.pyplot as plt

## Parameters
a = 1.0        # box length (in arbitrary units, set a=1)
hbar = 1.0     # reduced Planck constant (natural units)
m = 1.0        # particle mass (natural units)
N = 500        # number of spatial points

x = np.linspace(0, a, N)

## Normalisation constant and ground-state energy
A = np.sqrt(2 / a)
E1 = (np.pi**2 * hbar**2) / (2 * m * a**2)

## Plot settings
n_max = 4
fig, axes = plt.subplots(1, 2, figsize=(12, 7))
colors = ['#2563eb', '#16a34a', '#dc2626', '#9333ea']

for n in range(1, n_max + 1):
    psi_n  = A * np.sin(n * np.pi * x / a)
    prob_n = psi_n**2
    En     = n**2 * E1

    scale = 0.4 * E1   # visual amplitude scale

    # Left panel: wavefunctions offset by energy
    axes[0].plot(x, psi_n * scale + En,
                 color=colors[n-1], linewidth=2, label=f'$n={n}$')
    axes[0].hlines(En, 0, a, color=colors[n-1],
                   linewidth=0.8, linestyle='--', alpha=0.5)
    axes[0].text(a * 1.02, En, f'$E_{n}={n**2}E_1$',
                 va='center', fontsize=9, color=colors[n-1])

    # Right panel: probability densities offset by energy
    axes[1].plot(x, prob_n * scale + En,
                 color=colors[n-1], linewidth=2, label=f'$n={n}$')
    axes[1].hlines(En, 0, a, color=colors[n-1],
                   linewidth=0.8, linestyle='--', alpha=0.5)
    axes[1].text(a * 1.02, En, f'$E_{n}={n**2}E_1$',
                 va='center', fontsize=9, color=colors[n-1])

## Add walls
for ax in axes:
    ax.axvline(0, color='black', linewidth=3)
    ax.axvline(a, color='black', linewidth=3)
    ax.set_xlim(-0.05, a * 1.25)
    ax.set_xlabel('$x / a$', fontsize=12)
    ax.legend(loc='upper left', fontsize=9)

axes[0].set_title(r'Wavefunctions $\psi_n(x)$', fontsize=13)
axes[0].set_ylabel('Energy (offset)', fontsize=12)
axes[1].set_title(r'Probability densities $|\psi_n(x)|^2$', fontsize=13)

plt.tight_layout()
plt.show()
```

Observe in the plots:
- Each $\psi_n$ has $n-1$ interior nodes.
- $|\psi_n|^2$ has $n$ peaks for the $n$th state.
- For $n=1$ the probability is largest at the **centre** of the box; this is completely non-classical.
- For large $n$, the probability density oscillates rapidly and, on average, approaches the classical uniform distribution (the **correspondence principle**).

````

````{example} Numerically verifying expectation values

The code below computes $\langle x \rangle$, $\langle x^2 \rangle$, $\langle p \rangle$, $\langle p^2 \rangle$ and the uncertainty product $\Delta x\,\Delta p$ numerically using the trapezoid rule, and compares with the analytical results.

```{code-cell} Python
:tag: hide-input

import numpy as np

a    = 1.0
hbar = 1.0
m    = 1.0
N    = 10000
x    = np.linspace(0, a, N)
dx   = x[1] - x[0]
E1   = (np.pi**2 * hbar**2) / (2 * m * a**2)

print(f"{'n':>4} {'<x> num':>10} {'<x> ana':>10} "
      f"{'Δx num':>10} {'Δp num':>10} {'ΔxΔp/ℏ':>10} {'min=0.5':>10}")
print("-" * 70)

for n in range(1, 6):
    psi = np.sqrt(2/a) * np.sin(n * np.pi * x / a)

    # <x>
    x_exp = np.trapz(psi * x * psi, x)

    # <x^2>
    x2_exp = np.trapz(psi * x**2 * psi, x)

    # <p> using finite difference for d/dx
    dpsi_dx = np.gradient(psi, dx)
    p_exp = np.trapz(psi * (-1j * hbar * dpsi_dx), x).real

    # <p^2>
    d2psi_dx2 = np.gradient(dpsi_dx, dx)
    p2_exp = np.trapz(psi * (-hbar**2 * d2psi_dx2), x).real

    dx_val = np.sqrt(x2_exp - x_exp**2)
    dp_val = np.sqrt(p2_exp - p_exp**2)
    product = dx_val * dp_val / hbar

    print(f"{n:>4} {x_exp:>10.5f} {a/2:>10.5f} "
          f"{dx_val:>10.5f} {dp_val:>10.5f} {product:>10.5f} {'0.500':>10}")
```

The table confirms that $\langle x \rangle = a/2$ for all $n$, and $\Delta x\,\Delta p > \hbar/2$ as required by the Heisenberg uncertainty principle.

````

+++ { "page-break": true }
+++

(ch_pib_s_3d)=
## Particle in a 3D Box

The 1D result generalises elegantly to three dimensions. Consider a particle confined to a rectangular box with side lengths $a$, $b$, $c$ along the $x$, $y$, $z$ axes respectively:

$$V(x,y,z) = \begin{cases} 0 & 0 \leq x \leq a,\; 0 \leq y \leq b,\; 0 \leq z \leq c \\ \infty & \text{otherwise} \end{cases}$$

(ch_pib_ss_separation)=
### Separation of Variables

Inside the box, the TISE is:

$$-\frac{\hbar^2}{2m}\left(\frac{\partial^2}{\partial x^2} + \frac{\partial^2}{\partial y^2} + \frac{\partial^2}{\partial z^2}\right)\psi(x,y,z) = E\,\psi(x,y,z)$$

We seek a **product solution**: $\psi(x,y,z) = X(x)\,Y(y)\,Z(z)$. Substituting and dividing by $XYZ$:

$$-\frac{\hbar^2}{2m}\left[\frac{1}{X}\frac{d^2X}{dx^2} + \frac{1}{Y}\frac{d^2Y}{dy^2} + \frac{1}{Z}\frac{d^2Z}{dz^2}\right] = E$$

Each term depends on a different variable and their sum is a constant, so each term must individually be a constant:

$$-\frac{\hbar^2}{2m}\frac{d^2X}{dx^2} = E_x X, \quad -\frac{\hbar^2}{2m}\frac{d^2Y}{dy^2} = E_y Y, \quad -\frac{\hbar^2}{2m}\frac{d^2Z}{dz^2} = E_z Z$$

with $E_x + E_y + E_z = E$. Each equation is just a 1D PIB! Its solutions are known immediately.

(ch_pib_ss_3d_solutions)=
### Eigenfunctions and Energies

The normalised 3D wavefunctions are the products of three 1D wavefunctions:

$$\boxed{\psi_{n_x,n_y,n_z}(x,y,z) = \sqrt{\frac{8}{abc}}\sin\!\left(\frac{n_x\pi x}{a}\right)\sin\!\left(\frac{n_y\pi y}{b}\right)\sin\!\left(\frac{n_z\pi z}{c}\right)}$$

with quantum numbers $n_x, n_y, n_z = 1, 2, 3, \ldots$

The total energy is the sum of the three independent contributions:

$$\boxed{E_{n_x,n_y,n_z} = \frac{h^2}{8m}\left(\frac{n_x^2}{a^2} + \frac{n_y^2}{b^2} + \frac{n_z^2}{c^2}\right)}$$

```{important}
This result illustrates a general principle of quantum mechanics:

- If the wavefunction **factors** into independent parts, the system is called **separable**.
- The total energy is the **sum** of the independent energies.
- The total wavefunction is the **product** of the independent wavefunctions.

This principle applies whenever the Hamiltonian can be written as $\hat{H} = \hat{H}_x + \hat{H}_y + \hat{H}_z$ with no cross terms.
```

(ch_pib_ss_degeneracy)=
### Degeneracy

For a **cubic box** ($a = b = c$), the energy simplifies to:

$$E_{n_x,n_y,n_z} = \frac{h^2}{8ma^2}\left(n_x^2 + n_y^2 + n_z^2\right)$$

Different combinations of quantum numbers that give the same sum $n_x^2+n_y^2+n_z^2$ have the **same energy** but **different wavefunctions**. This is called **degeneracy**.

| State(s) | $n_x^2+n_y^2+n_z^2$ | Energy | Degeneracy |
|---|---|---|---|
| $(1,1,1)$ | 3 | $\frac{3h^2}{8ma^2}$ | 1 (ground state) |
| $(2,1,1),(1,2,1),(1,1,2)$ | 6 | $\frac{6h^2}{8ma^2}$ | 3 (1st excited) |
| $(2,2,1),(2,1,2),(1,2,2)$ | 9 | $\frac{9h^2}{8ma^2}$ | 3 (2nd excited) |
| $(3,1,1),(1,3,1),(1,1,3)$ | 11 | $\frac{11h^2}{8ma^2}$ | 3 |
| $(2,2,2)$ | 12 | $\frac{12h^2}{8ma^2}$ | 1 |

Degeneracy arises from the cubic symmetry of the box. If we broke the symmetry (e.g., made the box rectangular with $a \neq b \neq c$), the degeneracy would be **lifted** and formerly equal energy levels would split.

+++ { "page-break": true }
+++

(ch_pib_s_realworld)=
## Real-World Applications

The particle-in-a-box model, despite its simplicity, gives quantitatively useful predictions for real physical systems.

(ch_pib_ss_molecules)=
### Conjugated Molecules: $\pi$-electron Networks

The $\pi$ electrons in conjugated organic molecules are delocalised along the backbone of the molecule and experience a roughly flat potential between the ends of the conjugated chain. This makes the 1D PIB an excellent first approximation.

**Example: $\beta$-carotene** — the molecule responsible for the orange colour of carrots. It has a conjugated $\pi$-network approximately $L \approx 2.4\;\mathrm{nm}$ long, containing 11 $\pi$ bonds and therefore 22 $\pi$ electrons.

Because each orbital holds 2 electrons (spin up and spin down), the 22 electrons fill the lowest 11 levels. The **HOMO** (Highest Occupied Molecular Orbital) is $n_1 = 11$; the **LUMO** (Lowest Unoccupied Molecular Orbital) is $n_2 = 12$.

The **lowest energy electronic transition** (absorption energy) is:

$$\Delta E = E_{n_2} - E_{n_1} = \frac{h^2}{8m_e L^2}\left(n_2^2 - n_1^2\right) = \frac{h^2}{8m_e L^2}(144 - 121) = \frac{23h^2}{8m_e L^2}$$

Plugging in numbers:

$$\Delta E = \frac{23 \times (6.626\times10^{-34})^2}{8 \times (9.109\times10^{-31}) \times (2.4\times10^{-9})^2} \approx 1.9\;\mathrm{eV}$$

This corresponds to a wavelength $\lambda = hc/\Delta E \approx 650\;\mathrm{nm}$ — blue/green light is absorbed, so $\beta$-carotene appears orange. The experimental absorption maximum is $\approx 450\;\mathrm{nm}$; the discrepancy arises from the simplicity of our model, but the order-of-magnitude agreement is striking.

```{note}
In general, the longer the conjugated chain, the smaller $\Delta E$, and the more the molecule absorbs towards the red. This is why many biological pigments — from chlorophyll (green) to haemoglobin (red) — tune their colour by controlling the length of their $\pi$ network.
```

(ch_pib_ss_qdots)=
### Quantum Dots

A **quantum dot** is a nanoscale semiconductor crystal (typically a few nanometres across) in which electrons are confined in all three spatial dimensions. An electron in a spherical quantum dot of diameter $d$ can be approximated as a 3D PIB with a cubic box of side $d$.

The ground state has quantum numbers $(n_x, n_y, n_z) = (1,1,1)$; the first excited states are the three equivalent $(2,1,1), (1,2,1), (1,1,2)$ states. The transition energy is:

$$\Delta E = E_{211} - E_{111} = \frac{h^2}{8m_e d^2}\left[(1+1+4)-(1+1+1)\right] = \frac{3h^2}{8m_e d^2} \propto \frac{1}{d^2}$$

**Key result:** smaller quantum dots absorb higher energy (bluer) light. This is experimentally confirmed: CdSe quantum dots of diameter $2\;\mathrm{nm}$ emit blue light, while $6\;\mathrm{nm}$ dots emit red light. The size-tuneable colour is exploited in quantum dot LED displays, solar cells, and biological imaging.

```{code-cell} Python
:tag: hide-input

import numpy as np
import matplotlib.pyplot as plt

## Quantum dot colour as a function of size
h  = 6.626e-34   # J·s
me = 9.109e-31   # kg
c  = 3.0e8       # m/s

d_nm = np.linspace(1.5, 8.0, 300)     # dot diameter in nm
d    = d_nm * 1e-9                     # convert to metres

# Transition energy for 3D cubic PIB
delta_E = 3 * h**2 / (8 * me * d**2)  # in Joules

# Corresponding wavelength
lam_m  = h * c / delta_E              # metres
lam_nm = lam_m * 1e9                  # nanometres

# Visible range colour map (very approximate)
def wavelength_to_rgb(wl):
    """Map wavelength (nm) to approximate RGB colour."""
    if wl < 380 or wl > 750:
        return (0.5, 0.5, 0.5)
    elif wl < 440:
        r, g, b = (440-wl)/(440-380), 0, 1
    elif wl < 490:
        r, g, b = 0, (wl-440)/(490-440), 1
    elif wl < 510:
        r, g, b = 0, 1, (510-wl)/(510-490)
    elif wl < 580:
        r, g, b = (wl-510)/(580-510), 1, 0
    elif wl < 645:
        r, g, b = 1, (645-wl)/(645-580), 0
    else:
        r, g, b = 1, 0, 0
    return (r, g, b)

fig, ax = plt.subplots(figsize=(10, 4))

for i in range(len(d_nm)-1):
    wl   = (lam_nm[i] + lam_nm[i+1]) / 2
    col  = wavelength_to_rgb(wl)
    ax.fill_between(d_nm[i:i+2], 0, 1,
                    color=col, alpha=0.8)

ax2 = ax.twinx()
ax2.plot(d_nm, lam_nm, 'k-', linewidth=2.5)
ax2.set_ylabel('Absorption wavelength (nm)', fontsize=11)
ax2.set_ylim(0, 1200)

ax.set_xlabel('Quantum dot diameter (nm)', fontsize=11)
ax.set_ylabel('Emitted colour', fontsize=11)
ax.set_yticks([])
ax.set_title('Quantum dot: size controls absorption wavelength (3D PIB model)',
             fontsize=12)
ax.set_xlim(1.5, 8.0)

plt.tight_layout()
plt.show()
```

The plot illustrates that smaller dots absorb and emit shorter (bluer) wavelengths, while larger dots shift towards red. This is a direct consequence of $\Delta E \propto 1/d^2$.

+++ { "page-break": true }
+++

(ch_pib_s_numerical2)=
## Extended Numerical Exploration

````{example} Interactive: comparing quantum states

The code below allows you to compare any two quantum states side by side, including their wavefunctions, probability densities, and a numerical summary of their expectation values.

```{code-cell} Python
:tag: hide-input

import numpy as np
import matplotlib.pyplot as plt

def pib_analysis(n_list, a=1.0, hbar=1.0, m=1.0):
    """
    Plot wavefunctions and probability densities for multiple n values,
    and print expectation values.
    """
    N = 2000
    x  = np.linspace(0, a, N)
    dx = x[1] - x[0]
    E1 = np.pi**2 * hbar**2 / (2 * m * a**2)

    fig, axes = plt.subplots(2, len(n_list), figsize=(4*len(n_list), 7),
                              sharex=True)
    if len(n_list) == 1:
        axes = axes.reshape(2, 1)

    colors = plt.cm.viridis(np.linspace(0.15, 0.85, len(n_list)))

    print(f"\n{'n':>4} {'E_n/E_1':>8} {'<x>':>8} {'Δx':>8} "
          f"{'<p>':>8} {'Δp':>8} {'ΔxΔp/ℏ':>10}")
    print("-" * 60)

    for idx, n in enumerate(n_list):
        psi  = np.sqrt(2/a) * np.sin(n * np.pi * x / a)
        prob = psi**2
        En   = n**2 * E1

        # Expectation values
        x_avg  = np.trapz(psi * x * psi, x)
        x2_avg = np.trapz(psi * x**2 * psi, x)
        dx_val = np.sqrt(x2_avg - x_avg**2)

        dpsi   = np.gradient(psi, dx)
        p_avg  = np.trapz(psi * (-1j*hbar*dpsi), x).real
        d2psi  = np.gradient(dpsi, dx)
        p2_avg = np.trapz(psi * (-hbar**2 * d2psi), x).real
        dp_val = np.sqrt(abs(p2_avg - p_avg**2))

        print(f"{n:>4} {n**2:>8.1f} {x_avg:>8.4f} {dx_val:>8.4f} "
              f"{p_avg:>8.4f} {dp_val:>8.4f} {dx_val*dp_val/hbar:>10.4f}")

        # Plot wavefunction
        axes[0, idx].plot(x, psi, color=colors[idx], linewidth=2)
        axes[0, idx].axhline(0, color='gray', linewidth=0.5)
        axes[0, idx].fill_between(x, 0, psi,
                                   where=psi > 0, alpha=0.3, color=colors[idx])
        axes[0, idx].fill_between(x, 0, psi,
                                   where=psi < 0, alpha=0.3, color='red')
        axes[0, idx].set_title(f'$n={n}$,  $E={n**2}E_1$', fontsize=11)
        axes[0, idx].set_ylabel(r'$\psi_n(x)$' if idx == 0 else '')

        # Plot probability density
        axes[1, idx].plot(x, prob, color=colors[idx], linewidth=2)
        axes[1, idx].fill_between(x, 0, prob, alpha=0.3, color=colors[idx])
        axes[1, idx].set_xlabel('$x/a$', fontsize=11)
        axes[1, idx].set_ylabel(r'$|\psi_n(x)|^2$' if idx == 0 else '')

        # Mark nodes
        for j in range(1, n):
            node_x = j * a / n
            axes[0, idx].axvline(node_x, color='black',
                                  linewidth=1, linestyle=':', alpha=0.5)
            axes[1, idx].axvline(node_x, color='black',
                                  linewidth=1, linestyle=':', alpha=0.5)

    plt.suptitle('Particle in a 1D Box: Wavefunctions and Probability Densities',
                 fontsize=12, y=1.01)
    plt.tight_layout()
    plt.show()

pib_analysis([1, 2, 3, 4])
```

Dotted vertical lines mark the nodes of each wavefunction. Notice how the probability density for large $n$ begins to fill the box more uniformly — a manifestation of the **correspondence principle**.

````

````{example} Superposition state: time evolution

A general quantum state is a superposition of energy eigenstates. Unlike a stationary state, a superposition **does** evolve in time because the different components oscillate at different frequencies $\omega_n = E_n/\hbar$.

The code below shows the time evolution of the equal-weight superposition of the first two states:

$$\Phi(x,t) = \frac{1}{\sqrt{2}}\psi_1(x)\,e^{-iE_1 t/\hbar} + \frac{1}{\sqrt{2}}\psi_2(x)\,e^{-iE_2 t/\hbar}$$

```{code-cell} Python
:tag: hide-input

import numpy as np
import matplotlib.pyplot as plt

a    = 1.0
hbar = 1.0
m    = 1.0
N    = 500
x    = np.linspace(0, a, N)

E1   = np.pi**2 * hbar**2 / (2 * m * a**2)
E2   = 4 * E1
psi1 = np.sqrt(2/a) * np.sin(1 * np.pi * x / a)
psi2 = np.sqrt(2/a) * np.sin(2 * np.pi * x / a)

# Period of oscillation of the probability density
# (beats between E1 and E2)
T_beat = 2 * np.pi * hbar / (E2 - E1)

t_values = np.linspace(0, T_beat, 6, endpoint=False)

fig, axes = plt.subplots(2, 3, figsize=(12, 6), sharex=True, sharey=True)

for idx, t in enumerate(t_values):
    ax  = axes[idx // 3, idx % 3]
    phi = (psi1 * np.exp(-1j * E1 * t / hbar) +
           psi2 * np.exp(-1j * E2 * t / hbar)) / np.sqrt(2)
    prob = np.abs(phi)**2

    ax.plot(x, prob, 'b-', linewidth=2)
    ax.fill_between(x, 0, prob, alpha=0.25, color='blue')
    ax.set_title(f'$t = {t/T_beat:.2f}\,T_{{beat}}$', fontsize=10)
    ax.set_ylim(0, 4.5)
    ax.axhline(1/a, color='gray', linestyle='--',
               linewidth=0.8, label='classical uniform' if idx == 0 else '')

axes[1, 0].set_xlabel('$x/a$', fontsize=11)
axes[1, 1].set_xlabel('$x/a$', fontsize=11)
axes[1, 2].set_xlabel('$x/a$', fontsize=11)
axes[0, 0].set_ylabel(r'$|\Phi(x,t)|^2$', fontsize=11)
axes[1, 0].set_ylabel(r'$|\Phi(x,t)|^2$', fontsize=11)

fig.suptitle(r'Time evolution of $|\Phi|^2$ for $\Phi = \frac{1}{\sqrt{2}}(\psi_1 + \psi_2)$',
             fontsize=12)
plt.tight_layout()
plt.show()
```

The probability density **sloshes** back and forth between the two halves of the box with the beat period $T_{beat} = 2\pi\hbar/(E_2 - E_1) = 2\pi\hbar/(3E_1)$. This is a purely quantum interference effect with no classical analogue. A stationary state ($c_n = 1$ for one $n$, all others zero) would show no time-dependence in $|\psi|^2$.

````


```{figure} ./images/QM_teaching_3.gif
:width: 99%
:label: fig_superposition
:alt: 
This animation illustrates the fundamental idea of time evolution in quantum mechanics using a particle confined in a one-dimensional infinite potential well of width $a$.
We begin by visualizing the first two stationary eigenstates, $ \psi_1(x,t) $ and $ \psi_2(x,t) $. Individually, these states exhibit only a time-dependent phase (represented here through oscillatory modulation), and their probability densities remain static in time.
Next, we construct a superposition of these states:
$\psi(x,t) = \psi_1(x)e^{-iE_1 t} + \psi_2(x)e^{-iE_2 t}.$
Unlike individual eigenstates, the superposition leads to non-trivial time evolution. The interference between the two states generates a dynamically evolving wave pattern inside the well.
Finally, we examine the observable quantity, the probability density:
$|\psi(x,t)|^2.$ Here, the time dependence becomes physically meaningful — the probability distribution oscillates within the well, demonstrating how quantum dynamics emerge from superposition.
*[Visualization by Subhadip Biswas. Generated using [Manim](https://www.manim.community/).]*
```

+++ { "page-break": true }
+++

(ch_pib_s_summary)=
## Summary

In this chapter we have built the quantum mechanical framework from the ground up and applied it to the particle in a one-dimensional box. The key results are collected here.

**Eigenfunctions and energies of the 1D infinite square well ($0 \leq x \leq a$):**

$$\psi_n(x) = \sqrt{\frac{2}{a}}\sin\!\left(\frac{n\pi x}{a}\right), \qquad E_n = \frac{n^2 h^2}{8ma^2} = n^2 E_1, \qquad n = 1, 2, 3, \ldots$$

**Key physical features:**

- Energies scale as $n^2$; the ground-state energy $E_1 > 0$ is the **zero-point energy**.
- The $n$th state has $n-1$ interior **nodes**; higher energy $\Leftrightarrow$ more nodes.
- $\langle x \rangle = a/2$ for all $n$; the probability distribution is symmetric.
- $\langle p \rangle = 0$ for all $n$; the particle is a standing wave.
- $\Delta x\,\Delta p > \hbar/2$ — the Heisenberg uncertainty principle is satisfied, with the ground state giving the smallest product.
- As $n \to \infty$, the quantum probability distribution approaches the classical uniform distribution (**correspondence principle**).

**3D rectangular box:**

$$\psi_{n_x n_y n_z} = \sqrt{\frac{8}{abc}}\sin\!\left(\frac{n_x\pi x}{a}\right)\sin\!\left(\frac{n_y\pi y}{b}\right)\sin\!\left(\frac{n_z\pi z}{c}\right)$$

$$E_{n_x n_y n_z} = \frac{h^2}{8m}\!\left(\frac{n_x^2}{a^2} + \frac{n_y^2}{b^2} + \frac{n_z^2}{c^2}\right)$$

For a **cubic** box ($a=b=c$), states with the same $n_x^2+n_y^2+n_z^2$ are **degenerate**.

**Applications:**
- Conjugated $\pi$-electron molecules (e.g., $\beta$-carotene): $\Delta E \propto (n_2^2-n_1^2)/L^2$ gives the visible absorption wavelength.
- Semiconductor quantum dots: $\Delta E \propto 1/d^2$ — smaller dots emit bluer light.

```{note} What comes next
The infinite box is the starting point for understanding more realistic quantum systems:

- The **finite square well** (walls of finite height) introduces exponentially decaying wavefunctions outside the box and quantum tunnelling.
- The **quantum harmonic oscillator** describes vibrations in molecules and lattices.
- The **hydrogen atom** is a 3D problem with a Coulomb potential, yielding the famous $E_n = -13.6/n^2\;\mathrm{eV}$ spectrum.

All these systems reuse the same roadmap and the same conceptual tools developed in this chapter.
```

+++ { "page-break": true }
+++

```{important} Suggested Books

- D. J. Griffiths, *Introduction to Quantum Mechanics*, 2nd Ed. (Pearson)  
- H. C. Verma, *Quantum Physics* (Surya Publications)  
- R. P. Feynman, R. B. Leighton, and M. Sands, *The Feynman Lectures on Physics*, Vol. 3 (Narosa Publishing)  
- J. J. Sakurai, *Modern Quantum Mechanics* (Pearson)  
- B. H. Bransden and C. J. Joachain, *Quantum Mechanics*, 2nd Ed. (Pearson Education)  
- P. A. M. Dirac, *The Principles of Quantum Mechanics*, 4th Ed. (Oxford Science Publications)  
- C. Cohen-Tannoudji, *Quantum Mechanics*, Vols. I & II (John Wiley & Sons)  
- R. Shankar, *Principles of Quantum Mechanics*, 2nd Ed. (Springer)  
- A. I. M. Rae, *Quantum Mechanics*, 4th Ed. (IOP Publishing)  
- E. Merzbacher, *Quantum Mechanics*, 3rd Ed. (Hamilton Printing Company)  
- L. D. Landau and L. M. Lifshitz, *Quantum Mechanics: Non-Relativistic Theory*, 3rd Ed. (Butterworth-Heinemann)
```
(ch_pib_s_exercises)=
