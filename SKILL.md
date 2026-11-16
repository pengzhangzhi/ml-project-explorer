---
name: ml-project-explorer
description: Turn an ML paper, repository or project specification into one Archify artifact with exactly three views: a drill-down map of Task, Data, Modeling and Evaluation, the data pipeline, and the model pipeline. Use when asked to explain, map, summarize or visualize an ML paper, project or codebase, or when given an arXiv link and asked what the project does.
license: MIT
---

# ML Project Explorer

One Archify explainer page, three panels, nothing else. Depth lives inside the diagrams, not in extra panels.

Read `~/.claude/skills/archify/SKILL.md` and `~/.claude/skills/archify/references/explainer-authoring.md` first. Archify does the rendering; never hand-write HTML or a second viewer.

The page is user-facing, so every word on it — the conclusion, the reasons, the panel titles, node labels and sublabels, the cards — is written for a reader, not for a log. If a `writing` skill is installed, read it before drafting and follow it. Either way: the conclusion's first sentence must stand alone, a term the reader may not use gets its meaning alongside it, every number keeps its unit and what it was measured on, and a fact stated in one place is not rephrased in another.

## What you deliver

A single `.html` page with:

- the one-paragraph conclusion, up to four reasons, `@answer scope=...`, and `@source` lines (this is the document header, not a panel);
- **panel 1 — the four components**, a `tree` diagram whose branches expand on click;
- **panel 2 — data pipeline**, a `dataflow` diagram;
- **panel 3 — model pipeline**, an `architecture` diagram.

No results panel, no ablation tables, no caveat panels, no checklist. Facts that would fill those panels belong in a node `sublabel`, a diagram `card`, or the `scope` line. If a fact fits nowhere, it is not important enough for the page.

## Investigate the source first

For a paper, read the method, datasets, experiments and the appendix. For a repository, read the README, configs, data loaders, model definitions, training and eval scripts, and prefer the implementation over the documentation; note where they disagree. For a specification, separate decided from proposed.

Preserve exact names, counts, tensor shapes and terminology. Mark an absent fact `not specified` and never guess one. Distinguish what a paper reports, what you inferred from code, and what is only proposed.

## Panel 1: the four components

Use this hierarchy exactly, as a native `tree` spec file (not compact `@tree` rows, which cannot set `collapsed`):

```
ML Project
├── Task          → Problem Definition
├── Data          → Dataset · Data Preparation
├── Modeling      → Model · Training
└── Evaluation    → Metrics · Evaluation Protocol · Baselines
```

- Level 3 holds the technical facts, one fact per node: short `label`, detail in `sublabel`.
- Set `collapsed: true` on every level-2 node. The page then opens at root plus four components plus eight subcategories, and the reader clicks down to the facts. The Viewer draws a stacked card with the hidden count, has Expand all, and takes Enter, Space and arrow keys.
- Give a **level 4** to every leaf whose term a non-specialist would want explained — a metric, a loss, a split rule, an encoder. Two children each: what it actually measures or does, and the one caveat or number that changes how it reads. Set `collapsed: true` on that leaf too. This is the only way to put teaching detail behind a click, because the Viewer reads just one authored detail field per node (`sublabel`), so hover and the Semantic Passport can only repeat the card's own text.
- Set `layout.direction: "right"` once the tree has four levels. Downward layout grows the canvas sideways with every leaf and blows the legibility floor (a 70-node tree measured 7137px wide and failed at 2.1px text); rightward layout grows it with depth instead and passes.
- Attach `sources` to a node whose claim a reader would want to check. `sources` are repository file paths verified against `ARCHIFY_REPO_ROOT`, not URLs, so a paper with no checked-out code gets none.

## Panel 2: data pipeline

A `dataflow` spec: raw sources → filtering and labeling → train / validation / test. Put real counts and splits in sublabels. Give absent facts a node of their own (`"Validation split"`, sublabel `"not specified"`) only when the omission changes how a reader reads the results; otherwise say it in a card.

Stop at the splits. Downstream losses and metrics belong to panel 3 or to a card — a fifth stage pushes text below the legibility floor.

A `dataflow` node cannot drill down, so a stage whose meaning needs a sentence gets that sentence in a card, named so the reader can tie the two together.

## Panel 3: model pipeline

Compact `@architecture` rows: input representations → encoders, blocks, branches → output. Show the major operations, and tensor shapes where the source gives them. Mark train-only paths with the `security` variant so inference-time flow stays readable. Auto-placement follows the edges; do not plan coordinates.

## Archify constraints that cause most failures

Learned from real finalize runs. Respect them in the first draft and you will usually pass on the first try.

**Legibility sets the canvas size.** Sublabel text renders at a fixed size and shrinks with the page scale, so a wide diagram fails the 6px floor no matter how short the text. Keep a `dataflow` at **four stages or fewer** and `meta.viewBox` width near **1000**. Shortening a sublabel does not rescue an over-wide canvas; narrowing the canvas does.

**Node text budget.** At `width: 140` keep sublabels to about **23 characters** and labels to about 19. A sublabel needing more pixels than the node provides is a hard error that names the exact shortfall.

**Same-row flows need `route: "straight"`.** When two nodes share a row across adjacent stages, port fan-out introduces a 9px jog that fails the 16px interior-segment floor. A straight route removes it.

**A flow label includes its `classification`.** Both strings share one rect, so a long classification widens the label and collides with nodes. Keep the label to one or two words and the classification short, or move the detail into a card.

**Lift crowded labels with `labelDy`.** About `-34` clears the row for a label wider than the gap between two nodes. Use `labelAt` when an offset keeps colliding. Keep node heights uniform (`height: 58`) so rows align.

**Avoid crossings by ordering rows, not by routing.** Assign rows so flows run straight or fan out once. Reordering two rows beats adding `via` points.

**A fan-in crowds labels, not lines.** Two or three flows converging on one node put their labels in the same corridor, and no `labelDy` clears it — every offset collides with either the other label, the vertical segment, or a node. Merge the sources into one node where they mean the same thing, and otherwise take the exact `labelAt` the diagnostic suggests: it computes a free position, so it lands first time where guessing does not.

## Delivery

Give each run its own folder, then:

```sh
node ~/.claude/skills/archify/bin/archify.mjs finalize explainer candidate.archify.md <slug>.html --json
```

A non-zero exit is never success. Diagnostics name the line, the node and a repair; fix the draft and rerun the same command. Do not edit the delivered HTML.

To publish the page as a shareable artifact, prepend one line, because the artifact wrapper strips the root attributes that the page's own CSS keys off:

```sh
{ printf '<script>document.documentElement.setAttribute("data-style","swiss")</script>\n'; cat <slug>.html; } > publish.html
```

Publish `publish.html`. Archify's PNG, SVG and WebM export buttons work in the local file only; the artifact viewer blocks downloads.

## Reporting

Two or three lines: the conclusion, then the link. State the abstraction level, anything deliberately omitted, and any requested content dropped during layout repair.

Say "automated checks passed" and not "visually reviewed" unless you actually looked at the rendered page. For a proposal, keep confirmed decisions and open questions apart, and never present a plan as a result.
