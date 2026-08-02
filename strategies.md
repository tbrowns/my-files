# Artemis Maths — Strategy Index & Traps to Avoid

> **Companion to `guideline.md`.** The full strategy exposition lives in **`guideline.md` §13**; this file is the quick index plus the **hard do-not list**. The traps below are rejection triggers — a task can be perfectly "In Distribution" and still be rejected for one of them, so screen every draft against Part 2 *before* burning a run set. Local working doc; gitignored; never part of a submission.

---

## Part 1 — Strategy index (detail in `guideline.md` §13)

The with-paper model scores 8/8 whenever the paper makes the answer a **lookup** or a **one-step plug-in**. Every strategy manufactures a reasoning step that the paper *scaffolds but does not finish*, aimed at a **Tier-3 chain collapse**.

| # | Strategy | Model weakness exploited | Answer shape | Trap it must dodge (Part 2) |
|---|---|---|---|---|
| §13.0 | **Latent divergence** (the principle) | Model self-checks only the easy cases where naive = correct | — | — |
| §13.1 | **The reduction (many-to-one)** — *master lever* | Recall + one-step plug-in both need an atomic value | trace / coefficient / ratio / surviving term (small by construction) | Computational Scope — keep the assembly *light* |
| §13.2 | **Latent exception** (invisible at $q=1$ / at the excluded boundary) | Over-applies the general rule past its domain | small **nonzero** value via a different relation | Misleading Questions — state the index/specialization exactly |
| §13.3 | **Adjacent / shifted operator** ($b_i$ vs $x_i=b_i-\tfrac1{[2]}$) — *most direct evidence* | Drops an additive correction across a multi-step chain | value/eigenvalue after the shift | — |
| §13.4 | **Wrong-default convention** (signed/devil product; $[k]_{q^2}$ not $([k])^2$; parity sign) | Reverts to trained default convention | value forcing the non-default rule | Misleading Questions — define coined terms neutrally |
| §13.4b | **Spectrum-family confusion** (nonclassical vs classical eigenvalues) — *higher risk* | Conflates near-identical families | one pinned eigenvalue | Misleading Questions — airtight in-prompt definition of *which* spectrum |
| §13.5 | **Two-type composition** (stacking meta-lever) | Drops context at a reasoning-type handoff | reduction over a shifted, sign-fixed intermediate | Failure Exploitation — vary the *axis*, not just numbers |

**Priority:** lead with **§13.1 (reduction)** for every task — it fixes the answer-size problem structurally — and stack **§13.3** and/or **§13.4** onto it via **§13.5**, because two independent divergences on one chain land the *middle* of the 2–7/8 band, where a single trap gives you a fragile 7/8 or an 8/8.

---

## Part 2 — Traps that must be avoided (rejection triggers)

### 2.1 Computational Scope

Avoid questions involving **excessive computation or extremely large numbers**. The focus must be on **reasoning, derivation, or proof** — not arithmetic or brute-force calculation. **A human expert must be able to solve it without computational tools** (Matlab, R, Mathematica).

- **Applied to our strategies:** the reduction (§13.1) is powerful *because* the load is structural (which term survives, which sign, which spectrum), with the final arithmetic trivial. If your reduction needs a machine to evaluate, you have built the wrong reduction — pick a symmetric function / coefficient / collapsing sum whose surviving computation a person does by hand.
- **Self-test:** can you produce the golden answer with pen and paper in a few minutes once the *insight* is had? If not, shrink the parameters or change the reduction — do **not** just set the Tool-Use flag TRUE to paper over it. (Large numbers also collide with the ≤4-digit rule, §7.3.)

### 2.2 Failure Exploitation

Avoid exploiting the **same model failure across multiple tasks**. If the model consistently mishandles a particular concept (e.g. the structure of $p$-groups), do **not** create several questions that all test that one failure by minor numerical variation.

- **Applied to our strategies:** the seven levers in Part 1 are *distinct axes*, not one axis re-parameterized. Do not ship "shifted operator at $n=3$", "shifted operator at $n=4$", "shifted operator at $q=3$" as three tasks — that is one failure, thrice. Rotate the **axis** (reduction → boundary exception → convention → composition) and, ideally, the **paper**.
- **Self-test:** if two of your tasks would fail for the *same reason in the same transcript sentence*, they are the same task. Keep a per-campaign ledger of which failure axis each task rests on.

### 2.3 Misleading Questions

Do **not** intentionally mislead the model with **unusually strict interpretations of terminology**. The difficulty must come from *reasoning*, not from a wording gotcha the model reads reasonably and still "loses."

- **(Algebra)** "How many groups of order 2 are there?" — Model: 1. Writer: infinitely many (prompt never said "up to isomorphism"). ❌ The prompt is under-specified, not the model wrong.
- **(Topology)** "Let $X$ be the Sierpiński space and $Y$ a discrete space on two points. How many maps are there $X\to Y$?" — Model: 2. Writer: 4 (prompt never said "continuous maps"). ❌ Same defect.
- **Applied to our strategies:** every latent trap (§13.2–13.4) must be **fair** — the prompt states the exact index, specialization, convention, and *which* spectrum, so an expert seeing only the prompt + paper reaches the golden answer **and** would call the failure reasonable. The model must fail by mis-*reasoning* over a fully-specified question, never by a defensible alternate reading of an ambiguous one. If closing the second reading is what makes the model right, it was a misleading question — fix the wording, do not keep the gotcha.
- **Self-test (the two-reading hunt):** find any second defensible interpretation of the prompt and close it. If the failing runs split between two readings rather than reasoning right vs wrong, the task is measuring ambiguity, not reasoning (§9 Step F; §10 issue 5).

---

## Part 3 — One-line screen (run before every run set)

- **Reduction, not a value?** (§13.1) — else expect 8/8.
- **Divergence latent on every self-checked case?** (§13.0) — invisible at $q=1$ / small $k$ / generic index.
- **Trap at a two-type handoff?** (§13.5) — else it is a Tier-2 slip, not a Tier-3 collapse.
- **Light arithmetic, small answer?** (§2.1) — hand-solvable, every integer ≤4 digits.
- **A fresh failure axis?** (§2.2) — not last task's trap with new numbers.
- **Zero second readings?** (§2.3) — fair reasoning failure, not a terminology gotcha.
