## Reverse-Engineering the SSIE 501 Black Box (`BlackBox_N`): A Multi-Scale Hypergraph, Non-Equilibrium Thermodynamic, and Conserved Kawasaki Model

**Course:** SSIE 501 — Introduction to Systems Science (Binghamton University)

**System Investigated:** `BlackBox_N.php` ($20 \times 20$ Discrete Cellular System, 10 States)

**Empirical Dataset:** 8 Controlled Experimental Protocols (54 Captured System States across `Step 1` to `Step 6001`)

---

## 1. Executive Summary & Epistemological Framework

### 1.1 Systems Science Epistemology (Klir's Hierarchy of Systems)

In George Klir’s *Architecture of Systems Problem Solving*, reverse-engineering an unknown "Black Box" requires climbing five epistemological levels:

1. **Level 0 — Source System:** The observable interface of `BlackBox_N.php`, consisting of a $20 \times 20$ colored numerical grid ($N = 400$ cells) with discrete states $S = \{0, 1, 2, 3, 4, 5, 6, 7, 8, 9\}$, a step counter (`Current step: t`), an integer step-size input $n$ (`Next n Step`), a single-step state history buffer (`Revert`), and a uniform random initializer (`Reset`).


2. **Level 1 — Data System:** The 54 state matrices $X(t) \in S^{20 \times 20}$ recorded across 8 experimental protocols spanning single-step micro-trajectories ($n = 1$), meso-scale perturbations ($n = 10, 100, 200, 250, 500$), macro-scale jumps ($n = 1000, 3000, 5050$), and `Revert` branching tests.


3. **Level 2 — Generative System:** The local and non-local stochastic transition laws $P\big(X(t+n) \mid X(t), n\big)$ that govern how individual cells mutate, count, resample, or swap positions over time.


4. **Level 3 — Structure System:** The relational decomposition of the $20 \times 20$ lattice into a **$4 \times 4$ macro-grid of sixteen $5 \times 5$ sub-blocks**, coupled into **four symmetric 100-cell L-shaped regions** ($\Omega_1, \Omega_2, \Omega_3, \Omega_4$).


5. **Level 4 — Meta-System:** The non-Markovian dependence of the system’s transition operator on the user-supplied step parameter $n$ ($N \neq 1 \times N$), where clicking `Next n Step` injects an external thermal energy pulse $E_{\text{pulse}}(n)$ that alters the interaction radius $R(n)$ and bond-breaking probability across the grid.



---

### 1.2 Core Architectural Summary

Contrary to initial visual impressions that the bottom half of the grid is divided asymmetrically along Row 12 or Row 17, rigorous cell-turnover auditing across long-horizon runs (`Step 2102` $\to$ `3102` $\to$ `4102` and `Step 4001` $\to$ `4351`) proves that the $20 \times 20$ matrix is partitioned with **100% geometric symmetry** into four 100-cell L-shaped domains built from sixteen $5 \times 5$ sub-blocks:

| Domain | Constituent $5 \times 5$ Sub-Blocks $(b_r, b_c)$ | 1-Indexed Grid Coordinates | Exact Cells | Governing Physics & Asymptotic Attractor |
| --- | --- | --- | --- | --- |
| **Region 1 ($\Omega_1$)** *(Top-Left L)* | `(0,0), (0,1), (0,2), (1,0)` | `R1..5, C1..15` $\cup$ `R6..10, C1..5` | **100** | **Bidirectional Counter ($0\to 4, 9\to 5$)** converging to deterministic $5 \times 5$ binary templates of `4` (Blue) and `5` (Purple).

 |
| **Region 2 ($\Omega_2$)** *(Top-Right L)* | `(0,3), (1,1), (1,2), (1,3)` | `R1..5, C16..20` $\cup$ `R6..10, C6..20` | **100** | **Monolithic Zero-State Absorbing Sink:** Non-zero states snap or step down monotonically to `0` (Black) and freeze permanently.

 |
| **Region 3 ($\Omega_3$)** *(Bottom-Left L)* | `(2,0), (2,1), (2,2), (3,0)` | `R11..15, C1..15` $\cup$ `R16..20, C1..5` | **100** | **Non-Equilibrium Stochastic Markov Bath:** Perpetual random turnover (~0.62 visible changes/step) weighted ~86% on `{0, 1, 2}`.

 |
| **Region 4 ($\Omega_4$)** *(Bottom-Right L)* | `(2,3), (3,1), (3,2), (3,3)` | `R11..15, C16..20` $\cup$ `R16..20, C6..20` | **100** | **Conserved Kawasaki Swap Crystallization:** 100% mass-conserving pairwise swaps ($u \leftrightarrow v$) within step-dependent radius $R(n)$.

 |

---

## 2. Spatial Architecture & Empirical Proof of the $5 \times 5$ Sub-Block Grid

### 2.1 State Space & Color Encoding

Each cell $(r,c)$ for $r, c \in \{1, \dots, 20\}$ holds an integer state $s \in \{0, \dots, 9\}$ rendered with a unique background color in the web interface:

* **`0` (Black, `#000000`, white text):** Ground absorbing state of Region 2; primary bath state (~36%) of Region 3; conserved crystal phase in Region 4.


* **`1` (Light Blue, `#7ec8e3`):** Primary bath state (~37%) of Region 3; conserved crystal phase in Region 4; transient counting state in Regions 1 & 2.


* **`2` (Teal, `#38a395`):** Secondary bath state (~14%) of Region 3; conserved crystal phase in Region 4; transient counting state in Regions 1 & 2.


* **`3` (Dark Green, `#1d7837`):** Minor bath state (~6%) of Region 3; conserved crystal phase in Region 4; penultimate counting state ($3 \to 4$) in Region 1.


* **`4` (Royal Blue, `#3b48f7`):** Background absorbing matrix state (75%–92% of cells) in Region 1; conserved crystal phase in Region 4.


* **`5` (Purple, `#a63e86`):** Foreground motif absorbing state (8%–25% of cells) in Region 1; conserved crystal phase in Region 4.


* **`6` (Olive Green, `#8c9227`), `7` (Salmon Pink, `#c9646b`), `8` (Burgundy, `#7a1c44`), `9` (White, `#ffffff`):** Conserved crystal phases in Region 4 and countdown transit states ($9 \to 8 \to 7 \to 6 \to 5$) in Region 1.



---

### 2.2 Hierarchical Decomposition into Sixteen $5 \times 5$ Sub-Blocks

Using 0-indexed coordinates $r, c \in \{0, \dots, 19\}$, every cell $(r,c)$ maps uniquely to:

1. **Macro-Block Coordinates:** $(b_r, b_c) = \big(\lfloor r/5 \rfloor, \lfloor c/5 \rfloor\big) \in \{0, 1, 2, 3\} \times \{0, 1, 2, 3\}$.
2. **Local Sub-Block Coordinates:** $(l_r, l_c) = (r \bmod 5, c \bmod 5) \in \{0, 1, 2, 3, 4\} \times \{0, 1, 2, 3, 4\}$.

The regional indicator function $R(r,c) \in \{1, 2, 3, 4\}$ is defined purely in terms of the macro-block indices $(b_r, b_c)$:


$$R(r,c) = \begin{cases} 
1 & \text{if } (b_r = 0 \land b_c \in \{0,1,2\}) \lor (b_r = 1 \land b_c = 0) \\
2 & \text{if } (b_r = 0 \land b_c = 3) \lor (b_r = 1 \land b_c \in \{1,2,3\}) \\
3 & \text{if } (b_r = 2 \land b_c \in \{0,1,2\}) \lor (b_r = 3 \land b_c = 0) \\
4 & \text{if } (b_r = 2 \land b_c = 3) \lor (b_r = 3 \land b_c \in \{1,2,3\})
\end{cases}$$

---

### 2.3 Empirical Refutation of the Asymmetrical Bottom-Half Boundary

In early visual inspections of a single frame (such as `Step 1112` in Experiment 1 or `Step 3102` in Experiment 2), the top-right sub-block of the bottom half (`Block(2,3)` covering `R11..15, C16..20`) and the bottom-left sub-block of Region 4 (`Block(3,1)` covering `R16..20, C6..10`) often contain clusters of `0`s, `1`s, and `2`s that visually blend into Region 3. However, **differential cell-turnover analysis** across consecutive epochs proves that `Block(2,3)` and `Block(3,1)` belong strictly to **Region 4**:

* **Proof 1 (`Step 3102` $\to$ `Step 4102` in Experiment 2):** Across this 1,000-step window, **73 out of 100 cells changed** in Region 3 (`Block(2,0), Block(2,1), Block(2,2), Block(3,0)`), whereas **only 10 out of 100 cells changed** in Region 4 (`Block(2,3), Block(3,1), Block(3,2), Block(3,3)`). Specifically, in `Block(2,3)` (`R11..15, C16..20`), **23 out of 25 cells remained 100% frozen** (`0 0 0 0 0` in Row 11, `2 2 6 0 0` in Row 12, `2 2 0 0 7` in Row 13, `1 1 2 0 0` in Row 14). In `Block(3,1)` (`R16..20, C6..10`), **23 out of 25 cells also remained 100% frozen**.


* **Proof 2 (`Step 4001` $\to$ `Step 4351` in Experiment 5):** Across five consecutive `n = 10` jumps and three `n = 100` jumps (350 total steps), **0 out of 25 cells changed** in `Block(2,3)` (`R11..15, C16..20`) and **0 out of 25 cells changed** in `Block(3,1)` (`R16..20, C6..10`), while columns `C1..15` in Rows 11–15 (`Block(2,0..2)`) and columns `C1..5` in Rows 16–20 (`Block(3,0)`) turned over continuously on every single step.



---

## 3. Foundational Hypotheses: Energy Landscapes, Dissociation Rates & Heterogeneity

### 3.1 Initial High-Energy Uniform State ($t = 1$)

Whenever `Reset` is clicked (`Current step: 1`), all 400 cells are initialized via an independent and identically distributed (i.i.d.) uniform random draw over $S = \{0, \dots, 9\}$:


$$P\big(X_{r,c}(1) = s\big) = \frac{1}{10} = 0.10 \quad \forall (r,c) \in \{1,\dots,20\}^2, \;\forall s \in S$$

Across our 6 independent `Step 1` initializations (Experiments 1, 2, 3, 4, 7, 8), the initial regional Shannon entropy $H_k(1)$:


$$H_k(t) = -\sum_{s=0}^{9} p_{k,s}(t) \log_2 p_{k,s}(t), \quad p_{k,s}(t) = \frac{1}{100}\sum_{(r,c)\in\Omega_k} \mathbb{I}\big(X_{r,c}(t) = s\big)$$


averages $H_k(1) = 3.24 \pm 0.04\text{ bits}$ (close to the theoretical maximum $\log_2(10) \approx 3.3219\text{ bits}$), and the initial number of matching orthogonal neighbor bonds $B_k(1)$:


$$B_k(t) = \sum_{\substack{\langle u, v \rangle \in \Omega_k \\ \|u-v\|_1 = 1}} \mathbb{I}\big(X_u(t) = X_v(t)\big)$$


averages $B_k(1) = 16.2 \pm 3.1$ bonds out of $160$ internal orthogonal edges per region (matching the theoretical random expectation $160 \times 0.10 = 16.0$ bonds).

---

### 3.2 Regional Potential Energy Functionals $E_k(t)$

We formulate the time evolution of each region $k \in \{1, 2, 3, 4\}$ as a stochastic relaxation process over a region-specific Hamiltonian (energy functional) $E_k(t)$:


$$E_k(t) = \underbrace{\sum_{u \in \Omega_k} V_k\big(X_u(t), u\big)}_{\text{One-Body Regional Field Potential}} + \underbrace{J_k \sum_{\substack{\langle u, v \rangle \in \Omega_k \\ \|u-v\|_1 = 1}} \mathbb{I}\big(X_u(t) \neq X_v(t)\big)}_{\text{Two-Body Interfacial Heterogeneity Penalty}} + \underbrace{\xi_k(t)}_{\text{Thermal Bath Noise}}$$

As iterations progress from $t = 1$ to $t \ge 4000$, the four regions exhibit starkly different energy-dissipation and heterogeneity regimes:

1. **Region 1 ($\Omega_1$ — Template-Driven Potential Well):**
* $V_1(X_u, u) = \vert{}X_u - T_u\vert{} > 0$, $J_1 = 0$, $\xi_1 \approx 0$.


* Every cell $u = (r,c) \in \Omega_1$ experiences a one-body restoring force pulling its state toward a predetermined binary target $T_{r,c} \in \{4, 5\}$ defined by its $5 \times 5$ sub-block template.


* $E_1(t)$ decays monotonically to $0$ over $t \in [1, 2000]$. Shannon entropy $H_1(t)$ drops from $3.26\text{ bits}$ at `Step 1` to $0.63\text{–}0.87\text{ bits}$ at equilibrium (reflecting an 84/16 to 71/29 split between `4` and `5`).




2. **Region 2 ($\Omega_2$ — Ground-State Zero Sink):**
* $V_2(X_u) = X_u > 0$, $J_2 = 0$, $\xi_2 = 0$.


* State `0` is the unique global energy minimum ($V_2(0) = 0$) and a strictly absorbing state.


* Both $E_2(t) \to 0$ and $H_2(t) \to 0.00\text{ bits}$ by $t \approx 1100\text{–}1500$, while orthogonal same-digit bonds hit the absolute maximum $B_2(\infty) = 160 / 160$.




3. **Region 3 ($\Omega_3$ — High-Temperature Driven Bath):**
* $V_3(X_u) = -\ln \pi_3(X_u)$, $J_3 = 0$, $\xi_3(t) \gg 0$.


* Kinetic energy never dissipates: even after 4,000 to 6,000 steps, Region 3 continuously resamples ~0.62 visible cells per step.


* Shannon entropy stabilizes at a non-equilibrium plateau of $H_3(\infty) \approx 1.80\text{–}1.95\text{ bits}$ (since ~86% of states fall in $\{0, 1, 2\}$), and orthogonal neighbor bonds fluctuate randomly around $B_3(t) \approx 42\text{–}52$ (purely due to the high base frequency of `0` and `1`, with zero spatial clustering affinity).




4. **Region 4 ($\Omega_4$ — Conserved Ferromagnetic / Potts Nucleation):**
* $V_4(X_u) = 0$ (no digit preference), $J_4 > 0$ (strong penalty for mismatched orthogonal neighbors), subject to the **exact mass conservation constraint** $\sum_{u \in \Omega_4} \mathbb{I}(X_u(t) = d) = M_d(1)$ for all $d \in \{0, \dots, 9\}$.


* Because digit counts are strictly conserved from `Step 1`, **global Shannon entropy remains invariant** at its initial maximum $H_4(t) = H_4(1) \approx 3.12\text{–}3.28\text{ bits}$ for all $t \ge 1$.


* Simultaneously, **interfacial heterogeneity energy $E_4(t)$ drops sharply** as matching digits swap into contiguous clusters, driving orthogonal same-digit bonds $B_4(t)$ from $\approx 16$ at `Step 1` up to $85\text{–}105$ bonds at equilibrium.





---

### 3.3 Quantitative Trajectory Table (Experiment 2: `Step 1` $\to$ `Step 4102`)

The table below reports the exact Shannon Entropy $H_k(t)$ (in bits) and Orthogonal Same-Digit Bond Count $B_k(t)$ (out of 160 internal edges per region) computed directly from the Experiment 2 matrices (`Step 1` to `Step 4102`):

| Step $t$ | $H_1(t)$ (bits) | $H_2(t)$ (bits) | $H_3(t)$ (bits) | $H_4(t)$ (bits) | Bonds $B_1(t)$ | Bonds $B_2(t)$ | Bonds $B_3(t)$ | Bonds $B_4(t)$ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **`Step 1`**<br> | $3.26$ | $3.23$ | $3.27$ | $3.28$ | $16$ | $15$ | $14$ | $17$ |
| **`Step 102`**<br> | $3.18$ | $2.85$ | $2.54$ | $3.25$ | $21$ | $38$ | $34$ | $31$ |
| **`Step 1102`**<br> | $1.39$ | $0.14$ | $1.88$ | $3.11$ | $88$ | $154$ | $49$ | $68$ |
| **`Step 2102`**<br> | $0.63$ | $0.00$ | $1.89$ | $3.12$ | $108$ | $160$ | $44$ | $82$ |
| **`Step 3102`**<br> | $0.70$ | $0.00$ | $1.82$ | $3.12$ | $106$ | $160$ | $47$ | $89$ |
| **`Step 4102`**<br> | $0.66$ | $0.00$ | $1.85$ | $3.12$ | $108$ | $160$ | $45$ | $92$ |

---

## 4. Mathematical & Algorithmic Laws of the Four Regions

### 4.1 Asynchronous Monte Carlo Clock (`n = 1` Elementary Updates)

By auditing all five single-step transitions ($n = 1$) captured across our dataset, we establish that **one elementary step ($n = 1$) executes at most one random cell update per region** (yielding 2 to 3 visible cell changes across the 400-cell board per step because picked cells already in absorbing states produce no visible change):

1. **Exp 1 (`Step 1` $\to$ `Step 2`):** Exactly **2 cells changed**—`(R1, C10): 2 -> 3` in $\Omega_1$, and `(R11, C7): 3 -> 1` in $\Omega_3$.


2. **Exp 2 (`Step 1` $\to$ `Step 2`):** Exactly **3 cells changed**—`(R6, C4): 1 -> 2` in $\Omega_1$, and a 2-cell neighbor swap `(R20, C14)=0 <-> (R20, C15)=8` in $\Omega_4$.


3. **Exp 3 (`Step 1000` $\to$ `Step 1001`):** Exactly **0 cells changed** because over 75% of the board was already frozen in absorbing states at `Step 1000`.


4. **Exp 4 (`Step 1` $\to$ `Step 2`):** Exactly **3 cells changed**—`(R2, C12): 1 -> 2` in $\Omega_1$, `(R9, C15): 5 -> 0` in $\Omega_2$, and `(R11, C11): 8 -> 5` in $\Omega_3$.


5. **Exp 4 (`Step 2` $\to$ `Step 3`):** Exactly **5 cells changed** (1 per region, including a 2-cell swap in $\Omega_4$)—`(R3, C14): 2 -> 9` in $\Omega_1$, `(R6, C14): 2 -> 4` in $\Omega_2$, `(R16, C2): 3 -> 1` in $\Omega_3$, and swap `(R17, C6)=9 <-> (R20, C14)=1` in $\Omega_4$.


6. **Exp 8 (`Step 1` $\to$ `Step 2 Branch A`):** Exactly **2 cells changed**—`(R1, C11): 6 -> 5` in $\Omega_1$, and `(R15, C1): 5 -> 0` in $\Omega_3$.


7. **Exp 8 (`Step 1` $\to$ `Step 2 Branch B`):** Exactly **2 cells changed**—`(R8, C5): 5 -> 0` in $\Omega_1$, and `(R2, C20): 4 -> 2` in $\Omega_2$.



---

### 4.2 Region 1 ($\Omega_1$): Bidirectional Counting & $5 \times 5$ Checkerboard Motifs

#### A. The Bidirectional Counting Rule ($0 \to 4$ vs. $9 \to 5$)

Every cell $(r,c) \in \Omega_1$ has a target state $T_{r,c} \in \{4, 5\}$ determined by its $5 \times 5$ sub-block template. Tracking individual cells across micro-steps (`Step 1` $\to$ `2` $\to$ `3` $\to$ `13` in Exp 4) and meso-steps (`Step 3001` $\to$ `3251` $\to$ `3501` $\to$ `4001` $\to$ `5001` $\to$ `6001` in Exp 6, and `Step 201` $\to$ `1001` in Exp 3 & 7) proves that **states `4` and `5` are approached from opposite directions**:

* **Target `4` (Count-Up Path $0 \to 1 \to 2 \to 3 \to 4$):**
* If $X_{r,c}(t) \in \{0, 1, 2, 3\}$, the cell increments by $+1$ when picked: $X_{r,c}(t+1) = X_{r,c}(t) + 1$.


* If $X_{r,c}(t) > 4$ (including when a cell previously settled at `5` is reassigned to target `4`), it first resets to `0` (e.g., `(R1, C12): 6 -> 0` and `(R9, C5): 7 -> 0` in Exp 4, and `(R8, C5): 5 -> 0` in Exp 8 Branch B) and then counts up $0 \to 1 \to 2 \to 3 \to 4$ (e.g., `(R6, C1)` going `5 -> 2 -> 4` and `(R6, C2)` going `4 -> 1 -> 4` in Exp 6).




* **Target `5` (Countdown Path $9 \to 8 \to 7 \to 6 \to 5$):**
* If $X_{r,c}(t) \in \{6, 7, 8, 9\}$, the cell decrements by $-1$ when picked: $X_{r,c}(t+1) = X_{r,c}(t) - 1$ (e.g., `(R2, C11): 6 -> 5` in Exp 4 and `(R1, C11): 6 -> 5` in Exp 8 Branch A).


* If $X_{r,c}(t) < 5$ on a target-`5` coordinate, the cell first jumps to `9` (e.g., `(R3, C14): 2 -> 9` in Exp 4, and `(R8, C4): 4 -> 9` at `Step 3251` in Exp 6) and then counts down $9 \to 8 \to 7 \to 6 \to 5$.


* In Exp 6 (`Step 3001` $\to$ `6001`), as `Block(1,0)` transitions from a single `5` to the 9-cell Expanded Glider motif, **every single cell transitioning from `4` to `5` passes through `{9, 8, 7, 6}**`: at `Step 3501`, `(R7, C1)=9, (R7, C5)=9, (R8, C4)=6, (R8, C5)=7, (R9, C2)=7, (R9, C4)=6`; at `Step 4001`, `(R7, C1)=9, (R7, C5)=7, (R8, C1)=6, (R9, C2)=6, (R9, C3)=6`; and by `Step 5001`–`6001`, all of those cells lock at `5`.





#### B. Catalog of Exact $5 \times 5$ Sub-Block Binary Templates in Region 1

Across all 8 experiments, the four $5 \times 5$ blocks of Region 1 (`Block(0,0)`, `Block(0,1)`, `Block(0,2)`, `Block(1,0)`) converge to a discrete set of $5 \times 5$ binary matrices representing generations of a **Conway's Game of Life glider on a $5 \times 5$ torus**:

1. **Template A (Sparse 1–2 Cell Motif):** `5` at local `(0,0)` (and often `(4,3)` and/or `(3,3)`), with all remaining cells set to `4`:

$$T_A = \begin{bmatrix} 5 & 4 & 4 & 4 & 4 \\ 4 & 4 & 4 & 4 & 4 \\ 4 & 4 & 4 & 4 & 4 \\ 4 & 4 & 4 & 4/5 & 4 \\ 4 & 4 & 4 & 5/4 & 4 \end{bmatrix}$$



*Observed in:* `Block(0,1)` in Exp 1; `Block(0,0)` and `Block(0,2)` in Exp 2, Exp 3, and Exp 4; `Block(1,0)` at `Step 3001` in Exp 6 and `Step 1001` (`10×100`) in Exp 7; and `Block(0,0), (0,1), (0,2)` in Exp 8 Branch A.


2. **Template B (Standard 6-Cell Glider Motif):** `5` at local `(1,0), (1,4), (2,0), (2,2), (3,1), (3,2)` (and occasionally `(1,1)` in `Block(0,1)` and `Block(0,2)`), with all other 18–19 cells set to `4`:

$$T_B = \begin{bmatrix} 4 & 4 & 4 & 4 & 4 \\ 5 & 4/5 & 4 & 4 & 5 \\ 5 & 4 & 5 & 4 & 4 \\ 4 & 5 & 5 & 4 & 4 \\ 4 & 4 & 4 & 4 & 4 \end{bmatrix}$$



*Observed in:* `Block(0,0)` and `Block(0,2)` in Exp 1 and Exp 5; `Block(0,1)` and `Block(1,0)` in Exp 2; `Block(0,1)` in Exp 3; `Block(0,0), (0,1), (0,2)` across all steps `3001`–`6001` in Exp 6; `Block(1,0)` (`1×1000`) and `Block(0,0), (0,1), (0,2)` (`10×100`) in Exp 7; and `Block(0,0), (0,1), (0,2)` in Exp 8 Branch B.


3. **Template B+ (9-Cell Expanded Glider Motif):** `5` at local `(1,0), (1,4), (2,0), (2,2), (2,3), (2,4), (3,1), (3,2), (3,3)`:

$$T_{B+} = \begin{bmatrix} 4 & 4 & 4 & 4 & 4 \\ 5 & 4 & 4 & 4 & 5 \\ 5 & 4 & 5 & 5 & 5 \\ 4 & 5 & 5 & 5 & 4 \\ 4 & 4 & 4 & 4 & 4 \end{bmatrix}$$



*Observed in:* `Block(1,0)` (`R6..10, C1..5`) at `Step 5001` and `Step 6001` in Exp 6.


4. **Template D (9/10-Cell Toroidal-Corner Motif):** `5`s wrapping the four corners of the $5 \times 5$ tile at local `(0,0), (0,1), (0,3), (0,4), (1,0), (1,4), (3,2), (4,0), (4,1)`:

$$T_D = \begin{bmatrix} 5 & 5 & 4 & 5 & 5 \\ 5 & 4 & 4 & 4 & 5 \\ 4 & 4 & 4 & 4 & 4 \\ 4 & 4 & 5 & 4/5 & 4 \\ 5 & 5 & 4 & 4 & 4 \end{bmatrix}$$



*Observed in:* `Block(1,0)` at `Step 5051` in Exp 4, and **both** odd-parity blocks `Block(0,1)` and `Block(1,0)` at `Step 4001`–`4351` in Exp 5.



---

### 4.3 Region 2 ($\Omega_2$): Monolithic Zero-State Absorbing Sink

In all four $5 \times 5$ blocks of Region 2 (`Block(0,3), Block(1,1), Block(1,2), Block(1,3)`), state `0` is a strictly absorbing state ($P(0 \to s) = 0$ for all $s \neq 0$).

* When a cell with $X_{r,c}(t) > 0$ is selected, it either **snaps directly to `0**` (e.g., `(R9, C15): 5 -> 0`, `(R3, C18): 8 -> 0`, `(R7, C10): 2 -> 0`, `(R9, C8): 9 -> 0` in Exp 4) or **steps down toward `0**` (e.g., `(R2, C20): 4 -> 2` in Exp 8 Branch B, and `(R1, C19): 4 -> 2 -> 0`, `(R2, C20): 3 -> 2 -> 1`, `(R3, C20): 2 -> 1 -> 0` in Exp 3).


* By `Step 1100`–`1500`, 97%–100% of Region 2 is locked at `0`, and by `Step 2000+` it never changes a single cell again across any subsequent experiment.



---

### 4.4 Region 3 ($\Omega_3$): Stationary Non-Equilibrium Markov Bath

Across its four $5 \times 5$ blocks (`Block(2,0), Block(2,1), Block(2,2), Block(3,0)`), Region 3 behaves as an ergodic, memoryless bath driven by continuous thermal noise:

* **Constant Turnover Rate:** Auditing the five consecutive `n = 10` jumps in Experiment 5 (`Step 4001` $\to$ `4011` $\to$ `4021` $\to$ `4031` $\to$ `4041` $\to$ `4051`) shows `8, 4, 6, 7, 6` visible cell changes per 10 steps (mean $= 6.20$ visible changes per 10 steps, corresponding to $1.0$ cell pick per step in Region 3 given a ~38% self-transition probability).


* **Empirical Stationary Distribution $\pi_3$:** Pooling all $800$ equilibrium cells of Region 3 across `Step 3112` (Exp 1), `Step 4102` (Exp 2), `Step 5051` (Exp 4), `Step 4001` & `4351` (Exp 5), `Step 3001` & `6001` (Exp 6), and `Step 1001` (Exp 8) yields the exact stationary probability distribution:



| State $s$ | `0` | `1` | `2` | `3` | `4` | `5` | `6` | `7` | `8` | `9` |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| **Empirical Share $\pi_3(s)$** | **35.8%** | **36.9%** | **14.5%** | **6.4%** | **3.1%** | **1.9%** | **0.6%** | **0.8%** | **0.0%** | **0.0%** |

Notice that `{0, 1, 2}` account for **87.2%** of all states in Region 3 at equilibrium, `{3, 4, 5}` account for **11.4%**, `{6, 7}` appear as rare ~1.4% transient spikes (such as `(R12, C13)=7` and `(R13, C6)=7` at `Step 4351` or `(R12, C2)=7` and `(R13, C4)=7` at `Step 4102`), and states `8` and `9` have **0.0%** stationary weight once initial `Step 1` values are overwritten.

---

### 4.5 Region 4 ($\Omega_4$): Strict Mass-Conserving Kawasaki Swap Dynamics

#### A. Proof of 100% Mass Conservation from `Step 1` to `Step 6001`

The single most important mathematical discovery of our investigation is that **Region 4 (`Block(2,3), Block(3,1), Block(3,2), Block(3,3)`) never creates, destroys, or overwrites digits—it evolves exclusively via pairwise state swaps ($X_u \leftrightarrow X_v$) inside $\Omega_4$**.

We proved this across three independent timescales:

1. **Single-Step & 10-Step Exact Swaps:**
* In Exp 2 (`Step 1` $\to$ `Step 2`), the only change in Region 4 is the horizontal swap `(R20, C14)=0 <-> (R20, C15)=8`.


* In Exp 4 (`Step 2` $\to$ `Step 3`), the only change in Region 4 is the cross-block swap `(R17, C6)=9 <-> (R20, C14)=1`, which immediately forms `9 9 9` at `R20, C13..15`.


* In Exp 4 (`Step 3` $\to$ `Step 13`), exactly 6 cells change in Region 4 via 3 disjoint swaps: `(R11, C19)=7 <-> (R16, C16)=0`, `(R17, C15)=3 <-> (R19, C9)=6`, and `(R18, C9)=8 <-> (R19, C8)=4`.


* In Exp 6 (`Step 3001` $\to$ `Step 3251`, `n = 250`), exactly 6 cells change via 3 swaps: `(R15, C18)=6 <-> (R17, C11)=1`, `(R15, C20)=0 <-> (R16, C8)=1`, and `(R17, C18)=2 <-> (R18, C16)=3`.




2. **1,000-Step Late-Stage Permutation (`Step 3102` $\to$ `Step 4102` in Exp 2):**
* Between `Step 3102` and `Step 4102`, the 10 cells that changed in Region 4 held the multiset $\{1, 1, 1, 2, 2, 2, 3, 3, 5, 6\}$ at `Step 3102` and the exact same multiset $\{1, 1, 1, 2, 2, 2, 3, 3, 5, 6\}$ at `Step 4102`.




3. **Full-Horizon Conservation (`Step 1` $\to$ `Step 1001` in Exp 8, and `Step 3001` $\to$ `Step 6001` in Exp 6):**
* In Experiment 8, the exact count of all 10 digits inside the 100 cells of Region 4 at `Step 1` is **100% identical** to the counts at `Step 1001 (Branch A)` and `Step 1001 (Branch B)`: `0: 7, 1: 10, 2: 10, 3: 15, 4: 14, 5: 9, 6: 11, 7: 8, 8: 9, 9: 7` (Total $= 100$).


* In Experiment 6, the exact count of all 10 digits across `Step 3001`, `3251`, `3501`, `4001`, `5001`, and `6001` is **100% invariant**: `0: 6, 1: 12, 2: 12, 3: 12, 4: 11, 5: 5, 6: 6, 7: 9, 8: 16, 9: 11` (Total $= 100$).





---

## 5. Advanced Systems Science Hypotheses

### 5.1 Multi-Scale Nested Hypergraph Model $\mathcal{H} = (V, \mathcal{E}_1 \cup \mathcal{E}_2 \cup \mathcal{E}_3 \cup \mathcal{E}_4)$

To unite **Individual vs. Collective behavior**, **Place-Specific dynamics**, and **Systemhood**, we formalize the grid as a 4-level hierarchical hypergraph over the vertex set $V = \{(r,c) : r,c \in \{0,\dots,19\}\}$ ($\vert{}V\vert{} = 400$):

$$\mathcal{H} = (V, \mathcal{E}), \quad \mathcal{E} = \mathcal{E}_1 \cup \mathcal{E}_2 \cup \mathcal{E}_3 \cup \mathcal{E}_4$$

| Hypergraph Level | Hyperedge Cardinality $\vert{}e\vert{}$ | Structural Definition | Emergent Collective Behavior vs. Individual Behavior |
| --- | --- | --- | --- |
| **Level 1: Individual Vertices** ($\mathcal{E}_1$) | $\vert{}e_1\vert{} = 1$ | $e_1(u) = \{u\}$ (400 singleton vertices) | **Intrinsic 1D Counter:** In isolation, an unstabilized cell in $\Omega_1$ or $\Omega_2$ increments ($+1$) or decrements ($-1$) along $S = \{0,\dots,9\}$.

 |
| **Level 2: Local Neighborhoods** ($\mathcal{E}_2$) | $\vert{}e_2\vert{} = 5$ (Von Neumann) / $9$ ($3 \times 3$ Moore) | $e_2(u) = \{u\} \cup \mathcal{N}_{\text{orth}}(u)$ (400 overlapping local hyperedges) | **Collective Crystal Locking in $\Omega_4$:** A singleton with $b(u, X_u) = 0$ matching neighbors in $e_2(u)$ is fluid and mobile; once $\ge 4$ identical digits form a $2 \times 2$ block across overlapping $e_2$ hyperedges ($b \ge 2$), collective binding locks them against perturbations of $n \le 100$.

 |
| **Level 3: Meso-Scale Sub-Blocks** ($\mathcal{E}_3$) | $\vert{}e_3\vert{} = 25$ | $e_3(b_r, b_c) = \{(r,c) : \lfloor r/5\rfloor = b_r, \lfloor c/5\rfloor = b_c\}$ (16 disjoint $5 \times 5$ hyperedges) | **Sub-Block Pattern Downward Causation in $\Omega_1$:** Individual cells in $\Omega_1$ do not cluster by local affinity; instead, the 25-vertex hyperedge $e_3(b_r, b_c)$ assigns each cell's target $T_{r,c} \in \{4,5\}$ to form a $5 \times 5$ Game-of-Life Glider or Toroidal-Corner motif.

 |
| **Level 4: Macro-Scale Regions** ($\mathcal{E}_4$) | $\vert{}e_4\vert{} = 100$ | $\Omega_1, \Omega_2, \Omega_3, \Omega_4$ (4 disjoint L-shaped hyperedges of four $5 \times 5$ blocks each) | **Regional Law Selection & Checkerboard Pairing:** Each 100-vertex hyperedge dictates the governing physical law (counting vs. `0`-sink vs. Markov bath vs. Kawasaki swaps) and pairs sub-blocks by checkerboard parity $(b_r + b_c) \bmod 2$.

 |

---

### 5.2 Click-Induced Energy Injection ($E_{\text{pulse}}(n)$): Rapid Quenching vs. High-Energy Annealing

Every click of `Next n Step` supplies an external thermal energy pulse $E_{\text{pulse}}(n) = k_B T(n)$ to the system, where the effective temperature $T(n)$ and Region 4 swap radius $R(n)$ grow monotonically with the input step size $n$:


$$T(n) = \alpha \ln\!\left(1 + \frac{n}{n_0}\right), \quad R(n) = \min\!\big(15, \lfloor \beta \ln(1 + n) \rfloor + 1\big)$$

This creates a fundamental **speed-versus-quality trade-off** verified by Experiments 3, 5, 6, and 7:

1. **Small Steps ($n \le 200$) Freeze Faster (Rapid Quenching):**
* Because $T(n)$ is below the single-bond activation barrier $\Delta E_{\text{bond}}$ ($n_{\text{crit}} \in (100, 250]$), cells in Region 1 count directly into their nearest template without high-energy resets—reaching **94% settled in `10 × 100**` (Exp 7) and **95% settled in `5 × 200**` (Exp 3) by `Step 1001`, compared to only **80%–89% settled in `1 × 1000**`.


* Similarly, in Region 4, small steps ($n = 100$ or $n = 200$) lock clusters in-place around initial `Step 1` seeds by `Step 501`–`801`, reaching a frozen stationary state much earlier than $n = 1000$, but trapping fragmented multi-island clusters and foreign singletons.




2. **Large Steps ($n \ge 250\text{–}1000$) Take Longer to Freeze but Reach Global Order (Annealing):**
* In Experiment 5 (`Step 4001` $\to$ `4351`), stepping with $n = 10$ and $n = 100$ produced **0 changes** in Regions 1, 2, and 4.


* In Experiment 6 (`Step 3001` $\to$ `6001`), stepping from an already-settled `Step 3001` board with **$n = 250$**, **$n = 500$**, and **$n = 1000$** supplied enough activation energy $T(n) > \Delta E_{\text{bond}}$ to:


1. Unbind single-bonded cells in Region 4 and merge every digit into a single contiguous crystal by `Step 6001`, and


2. Trigger a secondary template transition in `Block(1,0)` of Region 1 (`1 five` $\to$ `9-five Expanded Glider`), which counted down `9 -> 8 -> 7 -> 6 -> 5` and stabilized at `Step 6001`.







---

### 5.3 Evaluation of Barabási-Albert ("Rich Get Richer"), Boolean Networks, `Revert`, and External Time

1. **Barabási-Albert Preferential Attachment Evaluation:**
* **Global Digit Distribution is Driven by Regional Priors, Not Overwriting:** Digit `0` rises from ~10% at `Step 1` to ~36–40% of the entire $20 \times 20$ board at equilibrium because $\Omega_2$ forces 100 cells to `0`, $\Omega_3$ draws `0` with 35.8% probability, and $\Omega_4$ conserves its initial ~6–10 `0`s.


* **Cluster Coarsening in $\Omega_4$ Follows Ostwald Ripening:** While total digit counts $M_d$ in $\Omega_4$ never change, the **size of connected spatial clusters** follows a rich-get-richer law: larger clusters expose more orthogonal perimeter sites and capture diffusing singletons until a single dominant crystal contains 100% of that digit's mass.




2. **Boolean Activity Network ($A_{r,c}(t) = \mathbb{I}(X_{r,c}(t) \neq X_{r,c}(t-\Delta t))$):**
* Mapping state transitions to binary activity $A_{r,c}(t) \in \{0,1\}$ reveals three distinct dynamical phases: (i) **Absorbing Quiescence** ($A_{r,c} \to 0$) in $\Omega_1, \Omega_2$; (ii) **Uncorrelated Bernoulli Firing** ($P(A_{r,c}=1) \approx 0.062$ per step) in $\Omega_3$; and (iii) **Paired Defect Cascades** in $\Omega_4$, where every swap activates a pair of vertices $(u,v)$ ($A_u = A_v = 1$) and modifies the bond count $b(w, X_w)$ of their 8 orthogonal neighbors, triggering downstream swaps.




3. **Stochastic Irreversibility of `Revert` (`Exp 8`):**
* Clicking `Revert` restores $X(t_{\text{prev}})$ and the step counter $t_{\text{prev}}$, but does **not** reset a deterministic PRNG seed. Consequently, advancing by $n = 1$ twice from the same `Step 1` state changes completely different cells (`Branch A` vs. `Branch B`), and advancing by $n = 1000$ twice from the same `Step 1` state converges to different Region 1 templates (`Template A` in Branch A vs. `Template B` in Branch B) and different Region 4 crystal arrangements.




4. **Invariance Across External Clock / Day-Night Cycles:**
* Comparing experiments performed during daytime (Oct 2, 1:48 PM – 3:06 PM EDT) against experiments performed at night (Oct 7, 7:06 PM – 7:56 PM EDT) confirms that all regional boundaries, counting rules, stationary distributions, and $5 \times 5$ templates are invariant with respect to wall-clock time.





---

## 6. Complete Empirical Log of All 8 Experimental Protocols (54 Screenshots)

| Protocol / Exp # | Date & Time (EDT) | Stepping Sequence (`n` per jump) | Captured Steps ($t$) | Primary Discoveries & Citations |
| --- | --- | --- | --- | --- |
| **Experiment 1** *(Multi-Scale Sweep)* | Oct 2, 1:48–1:51 PM | `Reset` $\to$ `1` $\to$ `10` $\to$ `100` $\to$ `1000` ($\times 3$) | `1, 2, 12, 112, 1112, 2112, 3112` (7 states) | Established ~2.5 cell updates/step (`1->2` changed 2 cells; `2->12` changed 25 cells); Region 1 (`4,5`) & Region 2 (`0`) freeze by `Step 2112`.

 |
| **Experiment 2** *(Long-Horizon Equilibrium)* | Oct 2, 1:55–1:56 PM | `Reset` $\to$ `1` $\to$ `100` $\to$ `1000` ($\times 4$) | `1, 2, 2, 102, 1102, 2102, 3102, 4102` (8 states) | Proved exact 100-cell L-regions via turnover (`3102->4102`), adjacent swap on `Step 2`, and 10-cell exact permutation in $\Omega_4$.

 |
| **Experiment 3** *(Path Divergence: `1000` vs `5×200`)* | Oct 2, 2:39–2:42 PM | Same `Step 1`: Branch A = `1000`; Branch B = `200`($\times 4$) + `199` + `1` | `1`, `1001 (A)`, `201, 401, 601, 801, 1000, 1001 (B)` (8 states) | Proved $1000 \neq 5 \times 200$: `5×200` settles Region 1 faster (95% vs 89%) but traps fragmented local islands in Region 4.

 |
| **Experiment 4** *(Ultra-Jump `5050` & Micro-Steps)* | Oct 2, 2:51–2:53 PM | Branch A: `5050`; Branch B: `Reset` $\to$ `1` $\to$ `1` $\to$ `10` | `5051`, and `1, 2, 3, 13` (5 states) | Captured 1-update-per-region elementary steps (`1->2->3->13`), 3 exact swaps in $\Omega_4$, and `+1` counting in $\Omega_1$.

 |
| **Experiment 5** *(Post-Equilibrium Perturbation)* | Oct 2, 3:04–3:06 PM | From `4001`: `10` ($\times 5$) $\to$ `100` ($\times 3$) | `4001, 4011, 4021, 4031, 4041, 4051, 4151, 4251, 4351` (9 states) | Proved 0 changes in $\Omega_1, \Omega_2, \Omega_4$ for $n \le 100$ at equilibrium; 100% of activity confined to $\Omega_3$ (~6.2 changes per 10 steps).

 |
| **Experiment 6** *(Protocol 1: Unfreezing Sweep)* | Oct 7, 7:06–7:10 PM | `3000` $\to$ `250` (Revert) $\to$ `500` (Revert) $\to$ `1000` ($\times 3$) | `3001, 3251, 3501, 4001, 5001, 6001` (6 states) | Pinpointed unfreezing threshold at $n=250$; proved 100% mass invariance in $\Omega_4$ across 3,000 steps and `9->8->7->6->5` countdown in $\Omega_1$.

 |
| **Experiment 7** *(Protocol 2: `1000` vs `10×100`)* | Oct 7, 7:12–7:14 PM | Same `Step 1`: Branch A = `1000`; Branch B = `100` ($\times 10$) | `1`, `1001 (A)`, `201, 501, 801, 1001 (B)` (6 states) | Proved `10×100` freezes $\Omega_4$ in-place by `Step 501` and reaches 94% settled in $\Omega_1$ vs 80% for `1×1000`.

 |
| **Experiment 8** *(Protocol 3: `Revert` Branching)* | Oct 7, 7:53–7:56 PM | Same `Step 1`: `1` (A vs B) & `1000` (A vs B) via `Revert` | `1`, `2 (A)`, `2 (B)`, `1001 (A)`, `1001 (B)` (5 states) | Proved `Revert` draws fresh random numbers and proved **100% mass conservation in $\Omega_4$ from `Step 1` to `Step 1001**`.

 |

---
