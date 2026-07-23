# Artemis Task Guidelines — Reference & Verification Guide

> **Purpose of this file.** This is a self-contained reference for writing and **verifying** Project Artemis tasks. If it is pasted into a fresh chat, it should be enough to bring an assistant fully up to speed so it can check a task against every rule and flag problems. The centerpiece is the **Verification Procedure** (Section 9) and the **Worked Example** (Section 10).

---

## 1. What an Artemis task is

An Artemis task is a **scientific question derived from a specific paper** (arXiv, bioRxiv, or medRxiv only) that has **exactly one unambiguous, verifiable answer**. It must require **reasoning with the paper's content** — applying, extrapolating, combining, or transforming it — not merely extracting a value.

The whole design turns on a difficulty gap:

- **Without the paper:** the model almost never solves it (**≤ 12.5%**, i.e. **0–1 of 8** runs correct).
- **With the paper:** the model solves it **sometimes but not always** (**25–87.5%**, i.e. **2–7 of 8** runs correct).

The paper supplies specialised information the model can use as a **hint or scaffold** toward the answer. The question then forces the model to **reason** to a new conclusion, so having the paper is *helpful but not sufficient*.

**Biology differs:** runs are 16 per set. Baseline target **0–2 of 16**; with-paper target **3–13 of 16**.

---

## 2. The difficulty target ("In Distribution")

Three model run sets, each attempting the task 8 times (16 in Biology):

| Run | Access | Standard target | Biology target | Meaning |
|---|---|---|---|---|
| **Baseline** | No paper, no web | **0–1 / 8** correct | **0–2 / 16** | Model rarely solves without the paper |
| **With Paper** | PDF of the paper | **2–7 / 8** correct | **3–13 / 16** | Model sometimes (not always) solves with the paper |
| **With Web Search** | Web only | No requirement | No requirement | Informational only |

- **Both** baseline and with-paper targets must be met to submit. The platform shows **"In Distribution"** in green when both pass.
- **8/8 with paper → under-stumped.** Add a reasoning step (Section 5). Do **not** just make it more obscure.
- **0/8 with paper → over-stumped or unclear.** Add a strategic hint, simplify one reasoning step, or fix ambiguity. Inspect model transcripts to see where reasoning breaks.
- Editing the **prompt field** after runs **invalidates all runs** — you must re-run all three sets. Up to **3 error runs** per set are tolerated if the rest are In Distribution.
- Explanation, notes, and reference-location fields can be edited **without** re-running — use them freely after runs (e.g. to record observed wrong answers). Treat **golden-response** changes like prompt changes: re-run everything.

---

## 3. The failure we are targeting: Tier 3

| Tier | Type | Examples | Counts? |
|---|---|---|---|
| 1 | Minor slips | Typos, phrasing, formatting | ❌ Too surface-level |
| 2 | Non-reasoning errors | Factual/arithmetic slips, retrieval errors, instruction-following failures | ❌ Knowledge/instruction gap, not reasoning |
| 3 | **Major reasoning failures** | Misconnected concepts, invalid logical leaps, multi-step chain collapse, flawed inferences | ✅ **This is the target** |

A Tier 3 failure is when the model **attempts a multi-step reasoning chain and breaks down inside it**. If the model would only fail by recalling a wrong fact or slipping in arithmetic, that is Tier 1/2 — revise to require more reasoning.

**The Gut Check (what reviewers ask before approving):** *"Does this question require the model to REASON, or just locate an obscure fact?"* Obscure paper content is fine and encouraged — but the question must **also** require reasoning with it. Using rare data AND reasoning = valid. Only testing whether the model can find/recall a rare fact = invalid.

---

## 4. Reasoning vs. recall (the core distinction)

If the answer is stated directly in the paper, having the paper is sufficient and the with-paper model scores ~100% — **out of distribution**. Fix by making the model *do something* with the value.

- ❌ **Recall:** "What is the $K_D$ of antibody A?" (answer is in the paper)
- ✅ **Reasoning:** "Using the binding parameters for antibody A, determine the fold-change in free antibody concentration required to shift fractional receptor occupancy from 20% to 80%." (paper gives the inputs; model must run a new calculation)

The paper should get the with-paper model **part way** — an advantage over baseline — while still leaving a reasoning step to complete. That is what lands the 25–87.5% band.

---

## 5. Building reasoning complexity

### The 10 reasoning types (layer these)

| Type | Definition |
|---|---|
| Deductive | Draw specific conclusions from general laws/principles |
| Inductive | Generalize from patterns or observations |
| Temporal | Predict events/states from ordering in time |
| Spatial | Structure, orientation, symmetry |
| Causal | Cause-and-effect relationships |
| Comparative | Judge between alternatives / evaluate differences across conditions |
| Abstract | Non-concrete or theoretical constructs |
| Pattern recognition | Spot and interpret regularities in data/sequences |
| Statistical | Data, probabilities, distributions |
| Hypothetical | Outcomes under counterfactual scenarios |

### The 6 strategies for adding complexity (use when 8/8 with paper)

1. **Reframe facts into reasoning challenges** — "given X, what follows when condition Y applies?" instead of "what is X?"
2. **Add layers of reasoning** — introduce a *second step of a different reasoning type* that consumes the output of the first. This is the main lever.
3. **Construct a realistic but hypothetical scenario** — plausible situation not in the literature, pinned down with specific values, so it can't be pattern-matched.
4. **Use exceptions to rules** — build on a known edge case/counterexample; models are biased toward the general rule and expose themselves on exceptions.
5. **Cross boundaries between disciplines or scales** — connect two compartments the model keeps separate (horizontal across subfields, or vertical across scales).
6. **Leverage your expertise** — write what only a specialist could formulate. If an intelligent non-specialist could write or answer it, it is probably too easy (especially "plug into a template" calculations).

> If still stuck after trying 3 strategies, post in the domain Slack channel with the task number and what you tried.

---

## 6. Task components

Every prompt has **three parts**:

1. **Context** required to answer (all constants, parameters, definitions the model needs).
2. **Formatting requirements** for the answer (units, sig figs, order, spacing, exact form).
3. **The question itself** (a single, unambiguous, interrogative ask).

---

## 7. Hard rules by field

### 7.1 Reference article

- Source is **arXiv, bioRxiv, or medRxiv only**.
- Each paper used for **one task only** (run **URL Uniqueness** → must show "Unique"; run **DOI validation** → green tick).
- Up to **2 articles** per task.
- PDF **under 100 pages**, **unaltered**, uploaded to Task Input Files.

### 7.2 Prompt

- ~**300 words** (no hard max), free of spelling/grammar errors.
- **Not** true/false, multiple-choice, or open-ended ("Explain…" is banned).
- **Ends in a single question** (interrogative).
- **Completely unambiguous** — only one defensible interpretation. State every required assumption/variable.
- **No undefined non-standard terminology (hard rule).** Every term, symbol, or named notion that is not standard across the field must be **fully defined in the prompt**. Terminology coined by the reference paper or its recent predecessors is **never** "standard," however natural it feels after reading the paper. Run the check: if a notion was introduced in the last few years and has only a handful of citations, it must be defined verbatim in the prompt or replaced by neutral notation defined there. A prompt that cannot even be *understood* without the paper is, in effect, dependent on the paper and fails self-containment — regardless of whether all numerical inputs are present.
- **Requires reasoning**, not just extraction.
- **Solvable without the paper**, even if very difficult (all needed numerical values stated in the prompt, **not** left for the model to pull from figures/tables).
- Contains **all answer-formatting requirements** with enough detail for an **exact string match**. Specify order if multiple values; specify units policy, sig figs, spacing, and exact form. Example directive: *"Answer in mg as a single integer to 2 significant figures, do not include units. Provide answers in alphabetical order, separated by a comma and space."*
- Must **pass the Task Similarity Check** (unlocks golden response + model runs).

### 7.3 Golden response

- **≤ 60 characters.**
- Numeric answers must be an **integer, reduced fraction, or radical** — **no decimals**.
- **Maximum 4 significant figures**, and — **hard rule, no judgment call** — **every integer appearing anywhere in the answer has at most 4 digits.** This applies to every numerator and every denominator of every fraction, and to every entry of a tuple. Exact reduced fractions are **not** exempt: `333125/512` fails on its 6-digit numerator. Choose the task's parameters so every final value complies **after full reduction**, and verify digit counts entry by entry before running. *(Historical note: the "exact fractions sit outside a sig-figs reading" defense in earlier drafts was ultimately rejected in review — do not rely on it; see Section 10, issue 10.)*
- **No units.**
- **Not** a commonly guessed value: `0, 1, −1, i, π, 2π, π/2, 90°, 180°, 360°, ±∞`.
- **Exact string** — mathematically equivalent forms fail autograding. Specify the required form in the prompt, then produce the golden response in exactly that form.

### 7.4 Explanation field

- Full step-by-step solution, **point form preferred**.
- **All intermediate steps and calculations shown**, with **why** each step is done.
- Enough detail for a reviewer to **follow the reasoning and independently reach the same answer**.
- **No rounding of intermediate steps** — carry full precision throughout.
- **Human-readable prose/bullets with LaTeX only around symbols and equations** — do **not** wrap the whole thing in LaTeX, and do not use `\texttt`, full table environments, or section titles inside it.
- **Argument only (hard rule).** The explanation is strictly an argument establishing the golden answer: the conventions used, each derivation step with its justification, exact arithmetic, lowest-terms verification, and cross-checks of the same values. It must contain **no speculation about model behavior** ("a model that … would produce …"), no distractor or trap commentary, and no grading strategy. Characteristic wrong answers and their mechanisms belong in the **notes field** (§7.5), never in the explanation.

### 7.5 Additional context / reference location field

- Specify the exact **page, figure, table, equation, or section** where relevant data appears.
- Plain text is fine (no LaTeX needed).
- Include anything else that helps the reviewer confirm the answer (normalization conventions, characteristic wrong answers, cross-checks).

### 7.6 Other supporting fields

- **Task Author:** exact RLS name (e.g. "EXP Melissa H").
- **Source Content Intact:** Yes (prompt does not contradict/modify the paper).
- **Answerable without paper:** confirmed.
- **Tool Use flag:** TRUE if solving needs Python/equivalent; FALSE if standard STEM reasoning suffices.
- **QA Job Report:** run it, then address (thumbs-up → fix → re-run) or dispute (thumbs-down → reason) every flag. For Reward Hacking Detection, thumbs-down with "This is an intentional part of task design."

---

## 8. Exact string match & LaTeX rules

### Non-equivalence — these pairs FAIL autograding

- `\boxed{1234}` ≠ `1234`
- `\frac{1}{2}` ≠ `0.5`
- `x+y` ≠ `y+x`
- `1,2,3` ≠ `1, 2, 3` (**spacing counts**)
- `10^6` ≠ `1e6`
- `1,2` ≠ `1, 2`

Specify the exact form in the prompt, then match it exactly in the golden response.

### Preferring pragmatic answer forms

Wherever possible avoid full symbolic expressions. Prefer:

- **Numeric substitution** (substitute all variables, return only the resulting number),
- **Evaluation tuples** (return evaluations at distinct fixed points — a tuple is one answer string, **not** multiple sub-questions),
- **A specific component** (a coefficient, a numerator, etc.).

If a **full expression** is unavoidable: define all variables/parameters/functions/domains in the prompt; combine like terms; fully expand products; order monomials lexicographically ($x > y > z$, descending total degree); write coefficients as **reduced rationals** (`p/q`, not decimals); use **no spaces inside LaTeX**. State all these form requirements in the prompt.

### LaTeX conventions (prompt & explanation)

- Use `$...$` for inline and `$$...$$` for display math. **Do not** use `\( \)` or `\[ \]`.
- Escape percent as `\%` (a bare `%` is a LaTeX comment).
- **Do not** define new macros. **Do not** number equations.
- Golden response LaTeX (if any) has **no spaces**.
- The **explanation** stays human-readable: LaTeX only around the math, no full-document scaffolding.

---

## 9. Verification procedure (use this to check a task)

Work through these in order. For each item, state **pass / fail / judgment call** and, on any fail, give the concrete fix. The order roughly follows the official reviewer workflow.

### Step A — Reference article
- [ ] Source is arXiv / bioRxiv / medRxiv only.
- [ ] PDF < 100 pages, unaltered.
- [ ] (If checkable) URL uniqueness "Unique", DOI green. *(Often not visible from text alone — note as "verify on platform.")*

### Step B — Prompt
- [ ] Ends in a **single interrogative question**.
- [ ] Not true/false, multiple-choice, or "Explain".
- [ ] **Unambiguous** — only one defensible interpretation; hunt for any second reading and close it.
- [ ] **All constants/values/assumptions stated in the prompt** (nothing the model must lift from a figure/table).
- [ ] **No undefined coined terminology** — every non-standard term/symbol is defined in the prompt or replaced by neutral in-prompt notation; notions coined by the reference paper (or its recent, low-citation predecessors) never count as standard.
- [ ] **Requires reasoning**, not extraction (apply the Gut Check).
- [ ] **Solvable without the paper** in principle (however hard).
- [ ] Answer format fully specified: units policy, sig figs, ordering, spacing, exact form — enough for an exact string match.
- [ ] LaTeX uses only `$`/`$$`; no `\(...\)`, `\[...\]`; `\%` escaped; no new macros; no numbered equations.
- [ ] Prose in the prompt renders cleanly on the platform (avoid `\texttt`, `\textbf`, `enumerate`, `~` if the field is plain-ish — prefer plain lists and plain text).
- [ ] Word count roughly in range (≈300; more is fine if needed for disambiguation — **do not** sacrifice clarity for brevity).

### Step C — Golden response
- [ ] ≤ 60 characters.
- [ ] Integer / reduced fraction / radical only if numeric; **no decimals**.
- [ ] ≤ 4 significant figures, **and every integer has at most 4 digits** — including all fraction numerators and denominators and all tuple entries; **no exemption for exact fractions**.
- [ ] No units.
- [ ] Not a disallowed common value.
- [ ] **Exact-string identical** to the form the prompt demands (spacing, delimiters, sign convention, no `\boxed`, no trailing text).

### Step D — Explanation & reasoning chain
- [ ] Expected failure is **Tier 3** (reasoning breakdown), not Tier 2 (arithmetic/knowledge slip). If the only plausible failure is a slip, the task needs more reasoning load.
- [ ] Every intermediate step shown, with **why**.
- [ ] **No rounded intermediates** — full precision throughout.
- [ ] A reviewer could **independently reproduce the answer** from it.
- [ ] Human-readable (bullets/prose), LaTeX only around math, no section titles / table environments inside.
- [ ] **Internally consistent** — every claim about the prompt's discriminators, checks, or constraints actually matches the prompt as written. *(This is a common, easily-missed failure — see Section 10.)*
- [ ] **No model-behavior speculation or distractor commentary** — the explanation argues for the answer only; observed or expected wrong answers live in the notes field.
- [ ] **Diagram-dependent claims verified at the diagram level** — if the paper's figures or displayed relations are vector graphics that text extraction silently drops, render the page; never infer which numbered relation/figure is which from surrounding prose alone. *(See Section 10, issue 5.)*
- [ ] Independently **recompute the answer** (by hand or code) rather than trusting the write-up.

### Step E — Supporting fields
- [ ] Reference location is **specific** (page/figure/table/equation/section).
- [ ] Notes flag characteristic wrong answers and any normalization conventions.
- [ ] Tool Use flag matches reality (Python needed → TRUE).
- [ ] Task Author exact; Source Content Intact; Answerable-without-paper confirmed.
- [ ] QA flags addressed or disputed with documented reasoning.

### Step F — Difficulty sanity check
- [ ] Predict roughly where runs will land. If it looks like 8/8 with paper, recommend a complexity lever (Section 5). If 0/8, recommend a hint or a de-ambiguation.
- [ ] Confirm the paper genuinely **helps** the with-paper model (gives a scaffold) without **handing over** the answer.
- [ ] After runs come back: check **why** the pass/fail split occurs — the score variance should come from the intended reasoning steps (the traps you designed), not from an ambiguity splitting two readings of the prompt. Read a passing and a failing transcript side by side.
- [ ] Note the **margin** to the band edges (7/8 passes by one run). A thin margin should shape how you negotiate reviewer-requested prompt edits, since any prompt edit forces full re-runs that can flip the distribution.

---

## 10. Worked example — a well-formed task

This is a real, verified task. Use it as the model of what "good" looks like, and note the **subtle issues** that were caught during review — those are the kinds of things to look for.

**Paper:** Bodish, Elias, Rose, *Type B Webs*, arXiv:2607.13252v1.

**Prompt (abridged idea, v1 — since revised, see issues 10–12 below):** Work in $\mathrm{Web}(\mathfrak{so}_{2n+1})$ with $n=3$, specialize $q=2$. Given the quantum-integer conventions $[m]=\frac{q^m-q^{-m}}{q-q^{-1}}$ and $[2]_m=q^m+q^{-m}$, evaluate four objects to exact reduced fractions: the spin circle $C_S=(-1)^{\binom{n+1}{2}}d_n$; the vector-loop-on-spin-strand coefficient $C_1$ (carefully disambiguated from the free-standing $1$-circle); the devil's product $P_{3,4}=$ "$[3][4]$"; and the flow-vertex denominator $F_3=$ "$[3]^2$" $=[3]_{q^2}$. Output exactly `(a/b,c/d,e/f,g/h)` with no spaces, leading minus for negatives, ending in a single question.

**Golden response (v1, later revised — see issues 10–12):** `(333125/512,-5461/160,1105/32,273/16)` (37 chars).

**Why it's a good Artemis task:**
- The paper gives only **symbolic conventions**; the $q=2$, $n=3$ rational values appear nowhere in it, so the with-paper model must still **instantiate + discriminate + compute**.
- Built on **exceptions to rules** (devil's arithmetic = signed exception to the conventions models default to) and **comparative** reasoning (four distractors).
- Every needed number is in the prompt → no figure/table extraction.
- Four-way exactness makes 8/8 unlikely and pure-recall impossible.

**Subtle issues caught in review (the instructive part):**
1. **Delimiter violation.** The draft used `\(...\)` / `\[...\]` and `\texttt` in the prompt — banned. Fix: convert all to `$`/`$$` and plain text/lists.
2. **Did not end in a question.** It ended on formatting rules with an imperative ("Determine…"). Fix: restructure to context → definitions → formatting → a final interrogative line.
3. **≤ 4 sig figs, judgment call.** `333125` has six significant figures. Defense: entries are **exact reduced fractions** (separately required), so a decimal "sig figs" reading arguably doesn't apply. Pre-empt in the notes field; be ready to dispute a QA flag; fallback is restructuring that slot only (which forces a re-run). *(Superseded — the defense was ultimately rejected; see issue 10 and the hard rule in §7.3.)*
4. **Internal inconsistency in the explanation.** The distractor note referenced a "negativity check" the prompt never states. The prompt's **actual** discriminators were "no $[2]_m$ in the final value" and "not the naive quantum dimension," and neither of those alone excludes the sign-dropped `+5461/160`; only the paper's $(-1)^n$ does. Fix: rewrite the note to cite the real discriminators and locate the parity sign in the paper's formula. **Lesson:** always check that claims *about the prompt* match the prompt verbatim.
5. **Definition/golden mismatch caught only by post-run QA.** The prompt's words described the *free-standing* $1$-circle while the golden value was the *loop-on-a-spin-strand* coefficient — the paper's relations (2.5)/(2.7) had been mapped to the wrong diagrams during authoring, because the diagrams are vector graphics that PDF text extraction silently drops, so the mapping was inferred from surrounding prose. The with-paper runs then split exactly 4/4 between the two readings — an "In Distribution" result produced entirely by the ambiguity rather than by the intended reasoning. Fix chosen: keep the golden response and reword the prompt to request the quantity that matches it (the smaller, truth-preserving edit). **Lessons:** (a) verify diagram-level claims *at the diagram level* — render the page if extraction drops figures; (b) when runs land In Distribution, check **why** the passing and failing runs differ, because a clean split can mask an ambiguity; (c) proof *structure* (which operations can produce which diagram types) is an independent check on which numbered relation is which.

**Distractor / characteristic wrong answers (good to record in notes):** `1119505/1024`, `+5461/160`, or `-1105/32` in slot 2 (the last one observed in a live with-paper run — see Final outcome below), `441/16` in slot 4, `-333125/512` in slot 1.

**Cross-check redundancy (good practice):** an identity in the paper tied two answer slots together, and one slot was confirmed three independent ways — cheap insurance the golden response is right.

**Final outcome (how the campaign ended):** run history across the iterations — scaffolded version: with-paper 4/8, but QA showed the split was entirely the ambiguity of issue 5, not the intended reasoning; disambiguated version: 8/8 (the prompt's hints plus the paper's redundancy made every slot a lookup); hint-stripped version: **7/8 — In Distribution at the upper boundary** — submitted. The one failing run answered `-1105/32` for $C_1$: that is $(-1)^n$"$[n][n+1]$" — the $k=n$ eigenvalue of the *shifted* operator $X = (\text{rung}) - \frac{1}{[2]}$, equivalently the paper's identity (2.11) with the $\frac{1}{[2]}$ identity-term dropped — off from the correct $-\frac{5461}{160}$ by exactly $\frac{2}{5}$. Conflating two adjacent operators mid-derivation is a textbook Tier 3 chain collapse: the strongest evidence a task can have that it measures reasoning rather than recall or arithmetic.

**Campaign lessons (sequel to issues 1–5):**

6. **Text-complete papers cap difficulty.** Transcript analysis showed the paper restates its key scalar in at least three independent plain-text places (the §2.2 propositions, a separate lollipop relation, and the §7 eigenvalue spectra), so blocking or de-hinting any single route changes nothing — models simply take another. Before committing to a paper, check whether the load-bearing quantities are stated *once* or *everywhere*; redundantly-stated reference papers minimize the with-paper reasoning gap by construction, and no prompt wording can restore it.
7. **Composites don't add difficulty in multiplicative settings.** Closed-diagram values factor into products or short signed sums of the paper's atomic scalars (the theta web here is exactly $C_1 \times \bigcirc_S$), so "ask for a composite instead" adds one lookup and one multiplication, not a reasoning step. Before spending a re-run cycle on a composite, check whether it factors through quantities the task already asks for or the paper already states.
8. **Know your margin.** 7/8 passes the band by a single run. With a thin margin, any reviewer-requested prompt edit (which forces full re-runs) risks flipping the task out of distribution — prefer explanation/notes-side resolutions where the reviewer's concern allows it, and say so in the review conversation.
9. **Non-prompt fields are free after runs.** Explanation, notes, and reference-location edits do not invalidate runs. Use them post-run to record *observed* wrong answers with their mechanisms (like the `-1105/32` diagnosis above) — that is exactly the evidence the reviewer's "expected failure is Tier 3" check wants. Treat golden-response changes like prompt changes: re-run.

**Post-submission review round (the v2 revision — issues 10–12).** A later review pass rejected three things the earlier rounds had let through. Each is now a hard rule in Section 7; the fixes below are the model for how to comply without destroying a task's difficulty structure.

10. **4-digit integer limit.** `333125` (six digits) in slot 1 violated the answer-size rule, and the issue-3 "exact fractions" defense was overruled. Fix: slot 1 was swapped from the spin circle (Eq. (1.3)) to the $S,S$-bigon on a vector strand — defining relation (1.2a) at $k=1$, value $(-1)^{\binom{3}{2}}[2]_1[2]_3 = -325/16$ — same rank, same paper conventions, slots 2–4 untouched, every answer integer now ≤ 4 digits. Shrinking the circle instead was impossible: at $q=2$ the rank-$n$ circle numerator is $\prod_{i=1}^{n}(4^{2i-1}+1)$ with no 2-adic cancellation (≥ 6 digits for every $n\ge 3$), and dropping to $n=2$ collides with Kuperberg's differently-normalized $B_2$ webs (his vertex is $[2]^{1/2}$ times the paper's, his $q$ is the paper's $q^2$), opening a second defensible reading. **Lesson:** when a slot breaks the size rule, look for a *sibling relation in the same convention system* with small values before changing parameters — parameter changes can import ambiguities from adjacent literature.
11. **Paper-coined terminology.** "Devil's arithmetic," "devil's central binomial coefficient," "devil's product," etc. were coined by the same authors in 2024 and cited only a handful of times since — nowhere near standard — yet the prompt used them undefined. That made the prompt unintelligible without the paper: a self-containment failure even though every numerical input was present. Fix: all coined terms were removed from the prompt; the alternating product is defined in-prompt as $[k]\star[\ell]$ with its full formula, and the two categorical slots are described purely diagrammatically. **Lesson:** citation-check every named notion in the prompt; if it traces to the reference paper's own circle, define it or neutralize it.
12. **Speculative content in the explanation.** The explanation contained model-behavior speculation ("a model that reaches the closed form … but drops the $(-1)^n$ produces …") and distractor commentary. Fix: rewritten as a pure argument (conventions → per-slot derivation → lowest-terms verification → cross-checks); all distractor material moved to the notes field, where issue-9 practice already said it belongs.

**v2 golden response:** `(-325/16,-5461/160,1105/32,273/16)` (34 chars; integers 325, 16, 5461, 160, 1105, 32, 273, 16 — all ≤ 4 digits). Prompt and golden both changed, so **all three run sets must be re-run**. Predicted difficulty: baseline still 0–1/8 (slots 1–2 require the paper's sign and normalization conventions, which the prompt deliberately does not state); with-paper near the top of the band, with a real 8/8 risk since the in-prompt $\star$ definition also hands the model the paper's Eq. (1.4)/(2.11) machinery. Pre-registered lever if 8/8 (Section 5): swap slot 4 — the weakest, a pure definition-application — for the (1.2d) fork coefficient $(-1)^{n-k+1}/[2]_{2n-2k+1}$ at $k=1$ (value $-32/1025$, rule-compliant), after wording-testing its diagram description against the issue-5 failure mode. Attaching the 2024 predecessor paper is **not** a difficulty fix: two articles are allowed (§7.1), but a second text source restating the same scalars only worsens the redundancy problem of lesson 6 and pushes toward 8/8.

---

## 11. Most common rejection reasons (check against these last)

- Prompt **modifies or contradicts** the reference paper.
- Question is **not self-contained** (needs the paper to answer at all).
- Access to the paper **does not significantly simplify** the problem (baseline too easy → out of distribution the other way).
- Question relies on **hidden/implicit context** from the paper.
- **Numerical values not in the prompt** — model expected to extract from figures/tables.
- **Output format not specified** (units, sig figs, rounding) → non-unique answers.
- **Explanation lacks calculations/steps.**
- **Undefined paper-coined terminology** in the prompt (fails self-containment even if every numerical input is present).
- **An answer integer exceeds 4 digits** (fraction numerators and denominators included).
- **Explanation speculates about model behavior** or catalogs distractors instead of arguing for the answer.
- **Intermediate steps rounded** (must carry full precision).
- **LaTeX guidelines not followed.**
- **Tool Use flag wrong** (needs Python but set FALSE).

---

## 12. Quick review template (paste-and-fill when verifying)

```
TASK: <id / short name>   PAPER: <source, arXiv id>

A. Reference article ....... pass / fail / verify-on-platform — notes:
B. Prompt
   - ends in a question ..... pass / fail
   - unambiguous ........... pass / fail   (second readings?)
   - values self-contained . pass / fail
   - no undefined coined terms pass / fail
   - requires reasoning ..... pass / fail   (Gut Check)
   - solvable w/o paper ..... pass / fail
   - format fully specified . pass / fail
   - LaTeX delimiters/macros. pass / fail
C. Golden response
   - ≤60 chars, exact form .. pass / fail
   - integer/fraction/radical pass / fail
   - ≤4 sig figs, all ints ≤4 digits pass / fail
   - no units, not disallowed pass / fail
D. Explanation
   - Tier 3 failure targeted. pass / fail
   - all steps + why ........ pass / fail
   - no rounded intermediates pass / fail
   - internally consistent .. pass / fail   (claims match prompt?)
   - no model-behavior speculation pass / fail
   - answer independently recomputed: <result> == golden? Y/N
E. Supporting fields ........ pass / fail — notes:
F. Difficulty prediction ..... likely baseline __/8, with-paper __/8; lever if needed:

VERDICT: ready / fix-then-ready / needs-rework
TOP FIXES: 1) ... 2) ... 3) ...
```

---

## 13. Maths-domain strategies for stumping the with-paper model

> **The problem this section solves.** With-paper models score **8/8** whenever the paper turns the answer into a *lookup* or a *one-step plug-in*. Every strategy below manufactures a reasoning step that **survives** the paper — the paper scaffolds it (so baseline stays ≤1/8) but does not finish it (so with-paper lands in 2–7/8) — and every one targets a **Tier-3 chain collapse**, not an arithmetic slip. These are distilled from observed model failure modes and must be built to respect the do-not list in `strategies.md`.
>
> **Running illustration:** arXiv:2607.13252v1 (*Type B Webs*) — the §10 task's paper. It is already consumed as a task (URL-uniqueness would fail a second time), so treat every example here as a **template to re-instantiate on a fresh paper**, not a second task on this one.

### 13.0 The one principle behind all of them (the "latent divergence" rule)

A with-paper model fails only when **the correct answer diverges from the model's default on exactly the cases the paper's friendly examples never exercise.** The model checks itself against the easy cases — small rank, $q=1$, generic position, $k=1$ — where the naive rule and the correct rule *agree*, so its own sanity checks pass while the chain is wrong. This is the maths incarnation of the Dynamo "latent case" lesson: **plant a determinate divergence where the model has no reason to look.** Design the divergence first; wrap a question around it second.

Three failure levers make a divergence latent:
- **Invisible at $q=1$** — a $q$-deformation factor that vanishes in the classical limit the model defaults to (the paper flags one explicitly: Rmk 2.8's rescaling $\frac{[2]_{2n-2k-1}}{[2]_{2n-2k+1}}$).
- **Invisible at the boundary the model skips** — an index where a general relation is *explicitly excluded* (the paper forbids (1.2d)/(1.2h) at $k=n-1$; (1.2e) is nonzero only at $k=n-2$).
- **Invisible because it agrees on small $k$** — a signed/alternating rule equal to the naive one at $k=1$ and diverging as $k$ grows (the devil's product (1.4) vs the ordinary product of Rmk 1.3).

### 13.1 The reduction (many-to-one) — the master technique

**Ask for a many-to-one *function* of the paper's atomic quantities, never an atomic quantity itself.** This is the single most valuable maths lever because it defeats **all three** ways an Artemis maths task fails at once:

1. **Kills recall / lookup.** No single value stated in the paper *is* the answer, so reading the paper cannot end the task.
2. **Kills the 8/8 plug-in.** A good reduction requires a *decision* the paper does not pre-make (which terms survive, which sign, which spectrum) — that decision is the reasoning trap. (Beware the §10-lesson-7 failure mode: a *product* or *short signed sum* of atomic scalars is **not** a reduction — it factors into one lookup + one multiplication. The assembly must require applying a relation, a cancellation, or a structural fact.)
3. **Kills the 4-digit answer-size problem structurally.** Raw web values explode to 6-digit numerators (§10 issue 10); a **symmetric function, a characteristic-polynomial coefficient, a trace/determinant, a reduced ratio, or a surviving single term** of a collapse stays small *by construction*. Stop hunting parameters to satisfy the size rule — pick a reduction whose output is inherently small.

Two flavours, both reasoning-driven (not arithmetic-heavy):

- **Collapsing reduction.** Pose a quantity defined as a sum/composite that *looks* like it needs every term, but the paper's **vanishing relations** kill all but one or two. *Hook:* the sum in (1.2c) precomposed with an $S$-cup — every term dies by (1.2b) except $k=n$ (this is exactly the paper's own proof of (2.5)). The reasoning is *which term survives*; the arithmetic is then trivial. Many-to-one, tiny answer.
- **Aggregate reduction (spectral).** The rung operator $b_i$ acts on the type-I-nonclassical module $S^{\otimes m}$ with eigenvalues $(-1)^{j-1}\frac{[2j-1]}{[2]}$, $1\le j\le n$ (Rmk 7.10). Ask for a **symmetric function** of the spectrum — trace, a power-sum, a char-poly coefficient of $p_{n+1}$ (Lemma 7.7), a determinant. *Worked example ($n=3,q=2$):* the trace is $\frac{[1]}{[2]}-\frac{[3]}{[2]}+\frac{[5]}{[2]}=\frac{2}{5}-\frac{21}{10}+\frac{341}{40}=\frac{273}{40}$ — clean, ≤4-digit. The Tier-3 trap: **drop the alternating sign** and you get $\frac{2}{5}+\frac{21}{10}+\frac{341}{40}=\frac{441}{40}$ — the same wrong-answer *family* the §10 task observed (`441/16`). That a naive execution reproduces the paper's own documented distractor is the strongest possible signal the reduction bites reasoning.

**Layer the reduction over §13.2–13.4 so it is not a stated lookup.** The bare spectrum is *in* the paper, so "trace of $b_i$" alone risks 8/8. Instead reduce over the **shifted** operator $x_i=b_i-\frac1{[2]}$ (§13.3), or over the braiding $\iota T_i=\sum_{k\ge0}q^{-k}x_i^{(k)}$ (7.8), so the model must first perform a derivation the paper only scaffolds.

### 13.2 The latent exception (invisible on the friendly case)

Exploits: models are **biased to the general rule and over-apply it past its stated domain** (guideline §5.4; Dynamo pattern A). Build the question at an index/parameter where the general relation is *explicitly excluded or degenerates*, so the naive answer (general formula) and the correct answer (boundary behaviour) diverge — but only there.

- *Boundary-index hook.* The paper states there is **no** (1.2d)/(1.2h) at $k=n-1$, and (1.2e) $=0$ **unless** $k=n-2$. A question resolved *at* $k=n-1$ or $k=n-2$ forces the boundary case; a model applying the generic relation gets a wrong nonzero value. (Design so the correct value is a small **nonzero** number obtained via a *different* relation — never $0$, which is a disallowed golden value §7.3.)
- *Invisible-at-$q=1$ hook.* Rmk 2.8: the "naive symmetry" resolution of (2.8) drops the factor $\frac{[2]_{2n-2k-1}}{[2]_{2n-2k+1}}$, which is $1$ at $q=1$. Ask for the value at a specialized $q$ where the correct and symmetric answers differ only by this factor. Any check the model runs at $q=1$ passes; the specialized answer exposes it.

Fairness (mandatory): the exception must be **derivable from the paper by an expert** (it is — the exclusions and the factor are stated), so the failure is reasoning, not a gotcha. This is *not* the misleading-terminology trap (`strategies.md`): the prompt states the exact index/specialization; only the *reasoning* about it is left open.

### 13.3 The adjacent / shifted operator (proven Tier-3 failure in this paper)

Exploits: models **track the salient object and silently drop an additive correction across a multi-step chain** (Dynamo "weights beat prose" reversion). The §10 task's one failing run did exactly this — it answered with the **shifted** operator $X=(\text{rung})-\frac1{[2]}$ where the correct object was the rung, off by exactly $\frac{2}{5}$ (`-1105/32` vs `-5461/160`).

- *Hook.* §7 is built on the pair $b_i$ (rung, eigenvalues $(-1)^{j-1}\frac{[2j-1]}{[2]}$) and $x_i=b_i-\frac1{[2]}$ (Lemma 7.6), with a whole recursion $x_i^{(k+1)}$ on top (7.6). Pose a derivation where a nearby, *more salient* operator differs from the required one by an additive shift or a normalization, and the correct value needs the shift carried through. The model grabs the salient one.
- **This is the strategy with the most direct evidence of working** on this exact material — prioritize it, and pair it with a reduction (§13.1) so the shift must survive an aggregation.

### 13.4 The wrong-default convention (signed exception the model un-signs)

Exploits: the model reverts to its **trained default convention** even when the prompt pins another (Dynamo B, wrong-default lure). Two hooks, both defined **neutrally in-prompt** (never with the paper's coined names — §10 issue 11):

- *Devil's product.* Define $[k]\star[\ell]:=[\ell+k-1]-[\ell+k-3]+\cdots+(-1)^{k-1}[\ell-k+1]$ in the prompt. The model's default is the **all-plus** ordinary product (Rmk 1.3). Equal at $k=1$; diverges as $k$ grows. A quantity forcing $\star$ traps the model that multiplies normally.
- *The squaring identity.* The paper's "$[k]^2$" means $[k]_{q^2}=\frac{[2k]}{[2]}$, **not** the literal square $([k])^2$. Neutralize the notation and require the $[2k]/[2]$ reading; a model computing $([k])^2$ diverges. (Verify the two disagree at your $q$ — they do for $k\ge2$.)
- *Parity sign.* The $(-1)^{\binom{\cdot}{2}}$ signs on (1.2a)/(1.2c)/(1.3)/(1.2d) are the classic dropped term. A reduction whose sign is fixed only by one of these parities catches the model that computes the magnitude correctly and loses the sign.

**Higher-risk variant — the spectrum-family confusion (§7.30–7.32).** The paper distinguishes three near-identical eigenvalue families: nonclassical $\frac{[2k+1]_q}{[2]_q}$, classical $\frac{[2k+1]_{iq}}{[2]_{iq}}$, and classical $[\ell]_{-q^2}$. Pinning one (neutrally) and requiring its value traps the model that reverts to the most familiar family. **Flag:** this rides close to the misleading-terminology line — only use it with an airtight in-prompt definition of *which* spectrum, so it is a reasoning distinction, not a wording gotcha. Run the fairness gut check hard.

### 13.5 The two-type composition (the stacking meta-lever — how a slip becomes a collapse)

A single divergence is often a Tier-2 slip. A **Tier-3 chain collapse** needs the divergence to sit at the *handoff between two reasoning types*, where models drop context. Build a two-step chain: **step 1** (say, algebraic — resolve a web / compute an eigenvalue) produces an intermediate; **step 2** of a *different* type (combinatorial/parity/comparative — §5.2) consumes it, and step 2's correct execution depends on a property of the intermediate (a sign, a vanishing, a parity) that holds *generically* but fails here. The model carries the generic property across the handoff. This is where §13.2–13.4 get *stacked*: e.g. a reduction (§13.1) over a shifted operator (§13.3) whose surviving term's sign is fixed by a parity (§13.4) — three independent divergences on one chain, exactly the "two axes that each bite the converged design" arithmetic that lands the middle of the band rather than 7/8 or 8/8.

### 13.6 Build checklist (maths tasks)

- [ ] The answer is a **many-to-one reduction**, not a stated atomic value (§13.1).
- [ ] The reduction's output is **inherently ≤4-digit** (symmetric function / coefficient / ratio / surviving term) — not parameter-hunted into compliance.
- [ ] There is a **determinate divergence** that is **latent** on every case the model self-checks ($q=1$, small $k$, generic index) — §13.0.
- [ ] The divergence sits at a **two-type handoff** so the failure is a chain collapse, not a slip (§13.5); confirm the expected wrong answer is Tier-3.
- [ ] Every coined term is **defined neutrally in the prompt** (§10 issue 11); the paper's names never appear.
- [ ] The failure is **fair**: an expert seeing only the prompt + paper reaches the answer and calls the failure reasonable (not a strict-terminology gotcha — `strategies.md`).
- [ ] Arithmetic is **light** — the load is in the reasoning, human-solvable without Mathematica (`strategies.md`, Computational Scope).
- [ ] Does **not** reuse a failure axis already spent on another task (`strategies.md`, Failure Exploitation).
- [ ] Predicted split lands **2–7/8** with a known margin; the intended reasoning steps — not an ambiguity — produce the variance (§9 Step F).

---

### One-line reminders

- **Reasoning, not recall.** Paper = scaffold, not answer key.
- **Target Tier 3.** A slip is not a win.
- **Exact string or it's wrong.** Spacing, delimiters, sign, no `\boxed`.
- **Full precision in the explanation.** No rounded intermediates.
- **Editing the prompt = re-run all models.** Batch prompt edits.
- **Check the explanation's claims against the prompt itself.**
- **Verify diagram claims at the diagram level.** Text extraction drops figures silently.
- **Check why runs split.** In Distribution via ambiguity is not really In Distribution.
- **Pick papers where the key content is stated once, not everywhere.** Text-redundant references cap the reasoning gap.
- **After runs, free fields first.** Explanation/notes edits cost nothing; prompt edits cost a full re-run.
- **Four digits max.** Every integer in the answer — numerators and denominators included. No exact-fraction exemption.
- **Define every coined term.** Paper jargon is never "standard."
- **Explanations argue; notes catalog.** Wrong answers and their mechanisms go in the notes field, never the explanation.
- **(Maths) Ask for a reduction, not a value.** Many-to-one output kills recall, kills 8/8, and keeps integers ≤4 digits — all at once (§13.1).
- **(Maths) Plant the divergence where the model can't look.** Invisible at $q=1$, at the excluded boundary, or where the signed rule still agrees on small $k$ (§13.0).
- **(Maths) Put the trap at a two-type handoff.** A one-step slip is Tier 2; a reasoning collapse is Tier 3 (§13.5).
- **(Maths) See `strategies.md` for the hard do-not list** — computational scope, failure reuse, and misleading-terminology gotchas.
