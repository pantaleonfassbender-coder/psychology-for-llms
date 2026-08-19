# LLM Psychology Explorer

An interactive, single-page companion to the survey **_A Review of Incorporating Psychological Theories in LLMs_** (Liu et al., EACL 2026). It catalogues the psychological theories the survey identifies, maps each one onto the stage of the LLM lifecycle where it is actually applied, and shows how much of psychology NLP has left untouched.

The whole site is one file — [`index.html`](index.html) — with no dependencies, no build step, and no network calls at runtime.

> **Scope.** This is an educational summary built from the survey and its appendix tables. It is not affiliated with the authors or the ACL, and it is not a substitute for the paper — consult the paper for the complete reference list and the full argument.

---

## What it does

The page has five parts, all driven by one in-file dataset.

### 1. Framework — four stages, six lenses

The survey maps psychological theory onto the full LLM lifecycle. The four stages are:

| Stage | What happens there |
|-------|--------------------|
| **Preprocessing** | Developmentally realistic data (child-directed speech, ecological validity) and cognitively inspired filtering. |
| **Pre-training** | Curriculum sequencing, top-down/bottom-up processing, language-acquisition framings. |
| **Post-training** | RLHF as operant conditioning; memory modules; persona and trait shaping. |
| **Evaluation & Application** | Psychometric instruments, theory-of-mind tasks, cognitive-load benchmarks. |

Clicking a stage card scrolls to the explorer with that stage's filter applied.

### 2. Theory Explorer

Every theory in the taxonomy as a filterable card grid. Filter by **subfield**, by **lifecycle stage**, by **exploration status**, or free-text search across names, authors, and descriptions. Clicking a card opens a modal with the theory's definition, how LLM work has used it, and the surveyed citations.

The six subfields, and how many theories each contributes:

| Subfield | Theories |
|----------|---------:|
| Social | 18 |
| Psycholinguistics | 9 |
| Cognitive | 8 |
| Personality | 8 |
| Developmental | 6 |
| Behavioral | 4 |
| **Total** | **53** |

### 3. Coverage — how much of psychology has NLP actually used?

A stacked bar per subfield, scoring each theory on how deeply the NLP community has explored it:

| Status | Meaning |
|--------|---------|
| ✓ **Established** | Multiple surveyed works explicitly leverage or reference the theory. |
| ◆ **Emerging** | Fewer than three surveyed works apply it — early days. |
| ✗ **Untapped** | No LLM work identified in the survey — an open opportunity. |

Social psychology is the starkest gap: 11 of its 18 theories have no identified LLM work.

### 4. Debates

Three accordions covering the survey's points of tension — terminology mismatches ("attention" and "memory" mean different things either side of the border), theoretical discrepancies (predictive coding, MBTI, RLHF's behaviorist blind spot), and evaluation validity (the GPT-4 false-belief result and what it does or doesn't show).

### 5. Opportunities

Four areas where the survey argues psychology could push LLM research further: social psychology, behavioral psychology, personality psychology, and schema theory.

---

## The data model

Everything the page renders comes from the `THEORIES` array inside the `<script>` block of `index.html`. One entry looks like this:

```js
{
  n:  "Operant Conditioning",                       // name
  f:  "Behavioral",                                 // subfield — one of the six
  c:  "Skinner (1957); Thorndike (1898)",           // originating citation
  s:  ["Post-training"],                            // lifecycle stages; [] = untapped
  st: "full",                                       // status: "full" | "some" | "none"
  d:  "Behavior is strengthened or weakened by …",  // the theory itself
  how:"RLHF operationalizes it: a learned reward …" // how LLM work applies it
}
```

Two small lookup objects sit alongside it:

- `FIELD_COLORS` — maps each subfield to its CSS custom property, so a new subfield needs a colour variable in `:root` as well as an entry here.
- `COVERAGE_ORDER` — the subfields in the order the coverage bars should read. Editorial, not alphabetical, which is the only reason it is written out by hand; the bar counts themselves are computed from `THEORIES`.

### Adding or editing a theory

1. Add an object to `THEORIES`. The card grid, the modal, the search index, the stage and status filters, the coverage chart, and the hero's theory counter all derive from the array — none of them need touching.
2. If it belongs to a new subfield, add a `--colour` variable in `:root`, an entry in `FIELD_COLORS`, a `.field-chip` in the explorer toolbar, and its position in `COVERAGE_ORDER`. A subfield missing from `COVERAGE_ORDER` still gets cards, but no bar.
3. The prose is not derived. The coverage lead names social psychology as the starkest gap ("11 of 18"), so check it still reads true after a status change.

---

## Running and deploying

There is nothing to install and nothing to build.

```bash
python -m http.server 8000
```

…then open <http://localhost:8000>. Opening `index.html` straight from the filesystem works too — there are no module imports or `fetch` calls to trip CORS.

Deployment is a static file drop: any static host serves it, and the site is published by dragging the repository folder onto [Netlify Drop](https://app.netlify.com/drop) or connecting the repository with no build command and the repository root as the publish directory.

### Browser support

Modern evergreen browsers. The page uses CSS custom properties, `backdrop-filter`, `clamp()`, and CSS grid; the script is plain ES5-era DOM code with template strings — no modules, no transpilation, no polyfills.

---

## Project layout

```
.
└── index.html    # everything: markup, styles, the theory dataset, and the interactions
```

Roughly: styles in `<head>`, the static sections (framework, explorer shell, coverage, debates, opportunities, footer) in `<body>`, and from `<script>` onward the `THEORIES` dataset followed by the render, filter, modal, chart, and accordion logic.

---

## Notes & caveats

- **Summaries, not sources.** Theory definitions, exploration statuses, and cited applications are condensed from the survey and its appendix. Where the survey flags a claim as contested, the card says so — but the paper is the authority.
- **The coverage chart counts the cards.** It is computed from `THEORIES` rather than transcribed a second time, so it cannot disagree with the grid. An earlier hand-entered version had drifted: it counted one psycholinguistics theory under cognitive, and scored another as untapped where its card says emerging. Correcting a status now means correcting the card's `st`, and the bar follows.
- **The hero's "40 theories" is a placeholder.** It is overwritten at load with `THEORIES.length`; the number in the markup is only what shows before the script runs.
- **No analytics, no tracking, no external requests.** Fonts are system stacks; nothing is fetched at runtime.

---

## Source

Liu, Z., Gong, Z., Ai, L., Hui, Z., Chen, R., Leach, C. W., Greene, M. R., & Hirschberg, J. (2026). A review of incorporating psychological theories in LLMs. _Proceedings of the 19th Conference of the European Chapter of the Association for Computational Linguistics_ (pp. 7459–7495).

A note on ethics, echoed from the survey's own limitations: reinforcement mechanisms that make systems engaging can also condition compulsive use. The authors call for transparent disclosure of reinforcement mechanisms and professional ethical guidelines, and acknowledge psychology's own critiques (WEIRD sampling, individual-level focus).

---

## License

No license file is included. All rights reserved by the author unless a license is added.

© 2026 — Dr. Pantaleon Fassbender — leo@twistersmanagementconsultingllc.com
