# Artemis task v6 — descent algebras, representation type × simple-module census

**Paper:** Erdmann–Lim, *The Representation Type of the Descent Algebras*, arXiv:2604.21619v1 (36 pp.)
**Regime:** Tools Required = TRUE. **Golden response:** `(26,23,169)` (11 chars)
**Status:** fresh paper, fresh failure axis — no reuse of the Type B Webs material.

---

## 1. Why this paper and not the Lights Out paper

Applying the post-mortem criterion — *the theorem must be the only bridge; a model must not be able to build and cross-check its way around it* — the two candidates separate sharply.

**2602.07241 (Lights Out) — rejected.** Its load-bearing result, Theorem 3.2, is a closed-form count: $|E^n|=2^{\binom{n-1}{2}}\prod_{i=1}^{\lfloor (n-1)/2\rfloor}(1-2^{1-2i})$. With the paper the task is one lookup plus arithmetic, which is precisely the 8/8 shape of v3–v5. Two further problems: extremality is decidable in polynomial time without the paper at all (a graph is extremal iff $A_G+I$ is invertible over $\mathbb{F}_2$ with $(A_G+I)\mathbf{1}=\mathbf{1}$, elementary linear algebra), so specific-graph questions have a live baseline; and the proposed $n=2$ boundary trap does not exist — the formula returns $1$ at $n=2$ and $n=1$, which is the correct answer in both cases (the empty graph on two vertices is even and has one matching). Nothing in the paper is both non-constructible and non-lookup.

**2604.21619 (descent algebras) — selected.** Its load-bearing content is a finite/tame/wild classification. That is the one kind of statement in either paper that a model **cannot** obtain by construction: deciding tameness versus wildness needs Drozd's theorem, separated-quiver criteria and Ext-quivers, and the authors themselves needed Magma plus a specialised idempotent construction for the small cases. Everything else about a descent algebra *is* constructible — structure constants, radical, simple counts — which is exactly why the task is built so that the constructible part alone cannot produce the answer.

## 2. Design

The answer partitions a census of twenty-four pairs $(W,p)$ into the three representation types and sums a **constructible** invariant (the number of simple modules) over each class. So:

- **Baseline is dead by construction.** A model without the paper can compute all twenty-four simple-module counts — that is elementary, if laborious, group theory — but cannot split them into finite / tame / wild. Guessing the split is hopeless: the correct partition is a specific 3-colouring of the grid in which the finite and tame cells are rare and irregular.
- **The paper does not contain the answer, or any part of it.** No sum, no census, no per-pair table of simple counts appears anywhere in the reference.
- **The reduction is many-to-one and inherently small**, so the ≤4-digit rule is met structurally.
- **Two reasoning types, with a handoff.** Step one is an algebraic classification judgment per pair; step two is a combinatorial enumeration per pair; the two must be paired correctly before summing. A correct classification with a wrong count, or a right count filed under the wrong type, both fail.

### The load-bearing divergence (the reason this can beat a careful reader)

For type $B_n$ the paper states that the simple modules are parametrised by the $p$-regular partitions of size at most $n$ (Prop. 3.7, with $p$-regular defined there as "does not have $p$ or more equal parts"). Applied verbatim at $p=2$ this yields $3$ simples for $B_2$ and $10$ for $B_5$. **Both are wrong.** Lemma 3.6(ii) gives $[\mathrm{N}_W(W_K):W_K]=2^{t}\prod_i a_i!$ where $t$ is the number of parts, so at $p=2$ the factor $2^t$ forces $t=0$ and only the empty partition survives: the true count is $1$ for every $n$. The paper's own Lemma 4.3 says exactly this — at $p=2$ the Ext-quiver of $\mathscr{D}_F(B_n)$ is *a single vertex* with at least $n$ loops — and Lemma 4.7(i) says $\mathscr{D}_F(B_2)$ at $p=2$ has a unique simple module. Prop. 3.7's parenthetical silently assumes $p$ odd; the authors handled the analogous $p=2$ collapse explicitly in type $D$ (Lemma 3.11) but not in type $B$.

I confirmed the value $1$ by brute force inside the Coxeter groups (see §5). So the task requires reconciling a loosely stated proposition against a precise lemma — a determinate reading with a determinate answer, not an ambiguity. A reader who takes Prop. 3.7 at face value produces `(26,25,178)`.

Three further divergences, all determinate and all stated once:

- **Type $D$ at $p=2$** collapses to $\{\varnothing\}$ for even $n$ and $\{\varnothing,(n)\}$ for odd $n$, not to the $p$-regular members of the general indexing set.
- **The $\pm$ doubling.** Partitions of $n$ with all parts even index *two* classes each; dropping this loses $3$ at $(D_6,5)$ and gives `(26,19,159)`.
- **The type $A$ index shift.** The paper's clauses are stated for $\mathscr{D}_F(A_{n-1})$ with conditions on $n$; a group of type $A_3$ needs $n=4$ and one of type $A_5$ needs $n=6$. The prompt fixes the rank convention explicitly, so the translation is determinate — but it must be performed, in both the classification and the count (where $s$ counts $p$-regular partitions of $m+1$, not of $m$).

## 3. Prompt

See `prompt_paste_ready.txt` (356 words, ends in a single interrogative).

## 4. Golden response

```
(26,23,169)
```

11 characters; integers 26, 23, 169 all ≤ 4 digits; no units; no disallowed value.

The full census (rows = group, cells = type / simple count):

| | p=2 | p=3 | p=5 | p=7 |
|---|---|---|---|---|
| $A_3$ | wild / 2 | finite / 4 | finite / 5 | finite / 5 |
| $A_5$ | wild / 4 | wild / 7 | wild / 10 | wild / 11 |
| $B_2$ | tame / 1 | finite / 4 | finite / 4 | finite / 4 |
| $B_5$ | wild / 1 | wild / 15 | wild / 18 | wild / 19 |
| $D_4$ | wild / 1 | wild / 10 | tame / 11 | tame / 11 |
| $D_6$ | wild / 1 | wild / 19 | wild / 25 | wild / 26 |

$f=26$, $t=23$, $w=169$; $26+23+169=218$ = total of all twenty-four counts.

## 5. Verification performed

- Every one of the twenty-four simple-module counts was recomputed **from first principles inside the Coxeter group**: build $W$ as a (signed) permutation group, enumerate all $2^{\text{rank}}$ standard parabolic subgroups, group them into conjugation orbits, and obtain $[\mathrm{N}_W(W_J):W_J]=|W|/(|\text{orbit}|\cdot|W_J|)$ by orbit–stabiliser. Ranks up to 7 ($|D_7|=322560$, $|B_7|=645120$) were checked. Brute force and the combinatorial formulas agree in every case, including all $p=2$ values.
- Independent agreement with the paper's own dimension identities: $B_4$ has $12$ vertices $+\,4$ arrows at $p\ge5$ and $10+6$ at $p=3$, both $=16=\dim\mathscr{D}_F(B_4)$ (Lemma 4.6); $B_5$ has $19$ vertices at $p\ge7$ and $18$ at $p=5$ (Lemma 4.9); $D_4$ at $p\ge5$ has $11$ simples against a $5$-dimensional radical, $11+5=16=\dim\mathscr{D}_F(D_4)$ (Lemma 6.3).
- The explanation's embedded code runs clean and reproduces `(26,23,169)`, including the group-theoretic confirmation loop.

## 6. Reference locations (notes field)

- Descent algebra, Solomon's subalgebra and $x_J$: §3.1, Eq. (3.1).
- Simple modules one-dimensional; radical spanned by $x_J-x_K$ ($J,K$ conjugate) and $x_L$ with $p\mid[\mathrm{N}_W(W_L):W_L]$: Theorem 3.1 (Atkinson–Pfeiffer–van Willigenburg / Solomon), with Lemma 3.3.
- Type $B$ normaliser index $2^{t}\prod a_i!$ and the parametrisation: Lemma 3.6(ii), Prop. 3.7. **The $p=2$ correction: Lemma 3.6(ii) forces $t=0$; confirmed by Lemma 4.3 (single vertex, $p=2$) and Lemma 4.7(i) (unique simple module for $B_2$ at $p=2$).**
- Type $D$ indexing set $\Gamma(n)$, the normaliser index $a\prod m_i!2^{m_i}$, and the explicit $p=2$ collapse: Lemma 3.11 and the paragraph following it; Prop. 3.12.
- Classification of representation type: the summary Theorem on p. 2, clauses (1)–(3); proved as Theorems 5.1 (type $B$), 6.1 (type $D$) and, for type $A$, quoted from Schocker and from the authors' earlier paper.
- Dimension cross-checks: Lemmas 4.6, 4.9, 6.3.
- Type $A$ normaliser index $\prod a_i!$ is classical (symmetric group), not specific to this paper.

## 7. Characteristic wrong answers and their mechanisms (notes field)

| answer | mechanism |
|---|---|
| `(26,25,178)` | Prop. 3.7 applied verbatim at $p=2$ in type $B$, ignoring the $2^{t}$ factor of Lemma 3.6(ii) |
| `(26,23,186)` | type $D$ $p=2$ collapse missed; general odd-$p$ rule applied at $p=2$ |
| `(26,19,159)` | $\pm$ doubling of all-even partitions dropped in type $D$ |
| `(26,29,189)` | partitions of size $\le n-1$ used instead of $\le n-2$ in the type $D$ indexing set |
| `(20,23,158)` | type $A$ index shift missed: $p$-regular partitions of $m$ counted instead of $m+1$ |
| `(26,74,118)` | type $D$ tame clause misread as "tame whenever $p\ge5$", dropping the $n=4$ restriction |
| any triple summing to $\neq218$ | a pair dropped or double-counted |

## 8. Difficulty forecast and margin

- **Baseline: 0/8 expected.** The three-way split is not derivable without the classification, and no partial credit exists under exact-string grading.
- **With paper: predicted 3–6/8.** The classification half is a careful but tractable read; the difficulty concentrates in the four count-level divergences, above all the type $B$ $p=2$ correction, which requires noticing that Prop. 3.7 and Lemma 3.6(ii)/Lemma 4.3 disagree at $p=2$ and resolving in favour of the lemma. A model that instead constructs the parabolic census itself will get the counts right — that route exists and is legitimate — but it must first suspect the summary proposition enough to check, and it must still get all twenty-four classification cells right, where no construction helps.
- **Pre-registered lever if 8/8:** replace the census grid with one that includes $H_3$ and $F_4$ (whose tame/wild verdicts at $p=2,3$ are the most isolated clauses in the classification) and require the ratio $w/t$ in lowest terms, so a single misfiled pair changes the answer non-locally.
- **Pre-registered lever if 0–1/8:** drop $p=2$ from the prime set, which removes the two hardest count corrections while leaving the classification census intact.

## 9. Open risk the author should weigh before submitting

The type $B$, $p=2$ value is the strongest discriminator in the task **and** its only review exposure. A reviewer who reads Prop. 3.7 in isolation may believe the golden response contradicts the reference. It does not — Lemma 3.6(ii), Lemma 4.3 and Lemma 4.7(i) all give $1$, and brute-force computation in $B_2,\dots,B_7$ confirms $1$ at $p=2$ — and §6 of the notes cites all three. If that argument is judged too fine to defend in review, removing $p=2$ from the prime set (the 0–1/8 lever above) eliminates the exposure at the cost of the task's sharpest edge; the golden response would then have to be recomputed and all run sets re-run.

## 10. Review checklist

```
TASK: descent algebras, rep-type x simple-module census   PAPER: arXiv:2604.21619v1

A. Prompt
   - ends in a question ..... pass
   - unambiguous ........... pass  (rank convention fixed explicitly; algebraically
                                    closed F; the three types are mutually exclusive
                                    and exhaustive by Drozd, stated in-prompt)
   - values self-contained . pass  (the 6 types, the 4 primes, all definitions)
   - no undefined coined terms pass (descent algebra defined from Solomon's
                                    construction; no notation from the reference used)
   - requires reasoning ..... pass  (classification judgments + parametrisation
                                    corrections; no stated value is any part of it)
   - solvable w/o paper ..... pass  (in principle)
   - not searchable online .. pass  (no census of this kind exists)
   - format fully specified . pass
   - LaTeX delimiters/macros. pass  ($...$ only, no new macros)
   - word count ............ 356
B. Golden response
   - <=60 chars, exact form .. pass (11 chars)
   - integer/fraction/radical pass
   - all integers <=4 digits . pass (26, 23, 169)
   - no units, not disallowed pass
C. Explanation
   - Tier 3 failure targeted. pass  (parametrisation/lemma reconciliation; index shift;
                                    classification-to-count handoff)
   - all steps + why ........ pass
   - no rounded intermediates pass  (exact integers throughout)
   - internally consistent .. pass
   - no model-behavior speculation pass (all of that lives in §7/§8 here, i.e. notes)
   - answer independently recomputed: (26,23,169) == golden? Y, two ways
D. Difficulty prediction ..... baseline 0/8; with-paper 3-6/8; levers pre-registered.

VERDICT: ready to run
```
