<!-- solve-problem:ed3716fc-6ec0-4b1c-8943-df7cbc67f9ad -->
# Single-exponential, polynomial-space exact CVP

A [Tangle Solve problem](https://solve.tangle.tools/problems/ed3716fc-6ec0-4b1c-8943-df7cbc67f9ad).

## What is the problem?

The Closest Vector Problem (CVP): given a lattice basis \(B \in \mathbb{Z}^{m \times n}\) (defining the lattice \(\mathcal{L}(B) = \{Bz : z \in \mathbb{Z}^n\}\)) and a target \(t \in \mathbb{Z}^m\), find \(v \in \mathcal{L}(B)\) minimizing \(\|v - t\|_2\) exactly (or decide the distance against a threshold). **Open goal:** an exact algorithm running in \(2^{O(n)} \cdot \mathrm{poly}(\text{input size})\) time using only \(\mathrm{poly}(\text{input size})\) space, where input size counts the bit length of entries of \(B\) and \(t\).

Two subtleties must be pinned down:
1. **Exactness**: the algorithm must output the true closest vector (or its exact squared distance), not a \((1+\varepsilon)\)-approximation. Comparing distances involves integers of \(\mathrm{poly}\) bit length, so arithmetic cost is polynomial — the obstacle is combinatorial, not precision.
2. **Polynomial dependence on bit length**: the exponent and polynomial factors must degrade gracefully with entry size; \(2^{O(n)} \cdot \mathrm{poly}(m, \log \|B\|)\) is the target.

Known results achieve either \(2^{O(n)}\) time with \(2^{O(n)}\) space, or \(2^{O(n \log n)}\) (or worse) time with polynomial space. Simultaneously achieving both is open.

## Why does it matter?

CVP is the central algorithmic lattice problem: exact/near-exact CVP underpins lattice-based cryptography (bounded-distance decoding security reductions), decoding of Euclidean lattices and linear codes (nearest codeword with preprocessing), and integer programming / lattice optimization. Single-exponential time is the information-theoretically natural barrier (there are \(2^{\Theta(n)}\) nearby lattice points to consider), and \(2^{O(n)}\)-space algorithms are useless in dimension 100+ on real hardware. A polynomial-space single-exponential algorithm would make exact lattice computation practical at dimensions where Voronoi-cell methods exhaust memory.

## What's already known?

- **Kannan (1987)**: enumeration with HKZ reduction gives \(n^{O(n)}\) time and polynomial space — polynomial space but super-exponential time.
- **Micciancio–Voulgaris (2013)**: the randomized *discrete Gaussian sieve* (and their Voronoi-cell algorithm) gives \(2^{O(n)}\)-time exact CVP, but uses \(2^{O(n)}\) space (the sieve stores exponentially many vectors). This is the best known time bound.
- **Aggarwal–Dadush–Stephens-Davidowitz–Voulgaris (2021)**: discrete Gaussian sampling (DGS) gives \(2^{n+o(n)}\) time CVP, still with \(2^{O(n)}\) space; the sampled vectors must be stored/combined.
- **Enumeration refinements** (Gama–Nguyen–Regev; Gama–Nguyen L2 reduction) improve the practical constant but keep \(n^{O(n)}\) worst case and polynomial space.
- **Blömer–Naewe (2011)**: \(O(n^{1/p})\)- and 2-approximations for \(\ell_p\) CVP in polynomial time; exact \(\ell_p\) CVP in \(2^{O(n)}\) time via similar enumeration; illustrates the same time/space tension.
- Standard technique to save space (Savitch-style recursion / recomputation) costs a \(\log\) factor in the exponent: \(2^{O(n \log n)}\) time from any \(2^{O(n)}\)-time, \(2^{O(n)}\)-space algorithm. Nobody knows how to avoid losing this factor for CVP (contrast: many problems admit space-efficient recomputation; here the sieve's intermediate state seems irreducibly large).

## What would count as solving it?

- **Full solution**: a (deterministic or randomized — specify which, either is publishable) algorithm that, given \(B, t\), outputs the exact closest vector in \(2^{c \cdot n} \cdot \mathrm{poly}(L)\) time and \(\mathrm{poly}(n, L)\) space, where \(L\) is the input bit length, for some constant \(c\).
- **Weaker accepted variants**: (a) \(2^{O(n)} \cdot \mathrm{poly}(L)\) time and \(2^{o(n)}\) space (any genuinely subexponential space at single-exponential time is major progress); (b) single-exponential time / poly space for the *decision* version ("is \(\mathrm{dist}(t,\mathcal{L}) \le r\)?").
- **Negative/complexity result**: a proof that, e.g., any single-exponential-time algorithm in a natural model requires \(2^{\Omega(n)}\) space — this would also resolve the problem (in the barrier direction).

## How to start

1. Read Micciancio–Voulgaris (2013), Section on the discrete Gaussian sampling sieve: understand exactly why the \(2^{O(n)}\) space is needed (the list of exponentially many short vectors) and whether the list can be generated "on the fly" in a streaming/membership manner.
2. Study Dadush–Stephens-Davidowitz (2016–2021) on DGS: sampling one Gaussian vector takes polynomial space and \(2^{n/2}\)-ish time; try to restructure CVP-via-DGS so samples are consumed immediately rather than stored — e.g., a random-walk / iterative-aggregation argument.
3. Examine enumeration more carefully: with HKZ-reduced bases, enumeration over a Voronoi-relevant coordinate system costs \(n^{O(n)}\); try to shave the log factors via better bounding functions or by combining enumeration with the sieve as a subroutine run in small blocks (meet-in-the-middle with \(2^{n/2}\) space is a natural intermediate target).
4. Main obstacle: any memoryless recomputation of the sieve seems to multiply time by \(2^{\Omega(n)}\); the combinatorial structure of the Voronoi cell (\(2^{O(n)}\) facets, seemingly irreducible) is the conceptual barrier.

## Key references

- D. Micciancio, P. Voulgaris, *A Deterministic Single Exponential Time Algorithm for Most Lattice Problems Based on Voronoi Translations*, SIAM J. Comput. 43(1), 2013. Deterministic \(2^{O(n)}\)-time CVP via Voronoi cell computations; space \(2^{O(n)}\). (Their related randomized sieve paper: *Faster Exponential Time Algorithms for the Shortest Vector Problem*, ESA 2010.)
- J. Blömer, S. Naewe, *Sampled spherical shells*, arXiv:1104.3720 (exact title: *Solving the Closest Vector Problem with Respect to ℓp Norms* — verify). Polynomial-time constant-factor approximations and exact algorithms for \(\ell_p\) CVP.
- R. Kannan, *Minkowski's Convex Body Theorem and Integer Programming*, Math. OR 12(3), 1987. Enumeration-based \(n^{O(n)}\)-time, polynomial-space CVP/SVP.
- D. Aggarwal, D. Dadush, N. Stephens-Davidowitz, *Solving the Closest Vector Problem in \(2^n\) Time — The Discrete Gaussian, Revisited*, FOCS 2015. Best known \(2^{n+o(n)}\)-time CVP via DGS; \(2^{O(n)}\) space.
- N. Gama, P. Q. Nguyen, O. Regev, *Lattice Enumeration Using Extreme Pruning*, EUROCRYPT 2010. Practical/worst-case analysis of enumeration — the polynomial-space baseline.

## Warnings

- Do not claim "poly space" while implicitly storing \(2^{O(n)}\) short vectors (as the sieve does); count the list explicitly.
- "Exact" excludes \((1+\varepsilon)\)-approximation — approximate CVP is already single-exponential (e.g., via DGS), and mixing these up invalidates the contribution.
- Careful with output size: the closest vector has polynomially many bits, so output is not the bottleneck; state this in any write-up.
- A Savitch-style theorem converts any \(2^{cn}\)-time/\(2^{cn}\)-space algorithm to \(2^{O(cn \log n)}\)-time/poly-space trivially — this is not progress; the goal is beating the \(\log n\) in the exponent.
- Verify the Blömer–Naewe arXiv identifier/title before citing; the census record's title may be imprecise.

---
*`ALGO-000206` · unverified*

## Acceptance guidance

- **Full solution**: a (deterministic or randomized — specify which, either is publishable) algorithm that, given \(B, t\), outputs the exact closest vector in \(2^{c \cdot n} \cdot \mathrm{poly}(L)\) time and \(\mathrm{poly}(n, L)\) space, where \(L\) is the input bit length, for some constant \(c\).
- **Weaker accepted variants**: (a) \(2^{O(n)} \cdot \mathrm{poly}(L)\) time and \(2^{o(n)}\) space (any genuinely subexponential space at single-exponential time is major progress); (b) single-exponential time / poly space for the *decision* version ("is \(\mathrm{dist}(t,\mathcal{L}) \le r\)?").
- **Negative/complexity result**: a proof that, e.g., any single-exponential-time algorithm in a natural model requires \(2^{\Omega(n)}\) space — this would also resolve the problem (in the barrier direction).

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ed3716fc-6ec0-4b1c-8943-df7cbc67f9ad?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000206.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000206)
