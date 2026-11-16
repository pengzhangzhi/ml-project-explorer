---
name: ml-project-explorer
description: Turn an ML paper, repository or project specification into one Archify explainer page with three panels — the data pipeline, the model pipeline covering both training and inference, and the evaluation pipeline. Use when asked to explain, map, summarize or visualize an ML paper, project or codebase, or when given an arXiv or bioRxiv link and asked what the project does.
license: MIT
---

# ML Project Explorer

One Archify explainer page, three panels, nothing else.

Read `~/.claude/skills/archify/SKILL.md` and `~/.claude/skills/archify/references/explainer-authoring.md` first. Archify renders; never hand-write HTML or add a second viewer.

**Concise at first glance, technically detailed when expanded.** A node's `label` is what a reader sees without acting: three or four words. The `sublabel` and `tag` are what Archify reveals on hover and on click, and that is where the shapes, counts and rates go. Implementation detail too long for a node goes in a `card`. Keep every exact name, count, tensor shape and rate the source gives; cut the explanation around them. Mark an unreported fact **Not specified** and never invent one.

If a `writing` skill is installed, read it before drafting.

## Deliver

- a document header: the one-paragraph conclusion, up to four reasons, `@answer scope=...`, `@source` lines;
- **panel 1 — data pipeline**, a `dataflow`: raw sources → processing → train / validation / test;
- **panel 2 — model pipeline**, a native `architecture` spec: input → components → output, with the training and inference paths on one canvas;
- **panel 3 — evaluation pipeline**, a `dataflow`: model outputs → scoring → final metrics.

No components tree, no results panel, no ablation tables, no caveat panels, no interactive layer. The task and problem definition belong in the conclusion and reasons. A fact that fits no node, card or the `scope` line is not important enough for the page.

## Investigate first

Paper: method, datasets, experiments, appendix. Repository: README, configs, data loaders, model definitions, training and eval scripts — prefer the implementation over the documentation and note where they disagree. Specification: separate decided from proposed.

Distinguish what the source reports, what you inferred from code, and what is only proposed.

## Panel 1: data pipeline

`dataflow`: raw sources → filtering, labeling, cropping → the splits with their real counts. Stop at the splits; losses belong to panel 2 and metrics to panel 3.

Give an absent fact its own node (`"Validation split"`, sublabel `"Not specified"`) only when the omission changes how the results read; otherwise put it in a card. Say how train and test were separated, and how they were not — an entry-level exclusion with no homology split is a fact a reader needs beside the benchmark numbers.

## Panel 2: model pipeline

Compact `@architecture` rows. One canvas carries both paths:

- **Shared spine**: input representation → encoders, blocks, branches → output.
- **Inference path**: mark it `emphasis`, so a reader sees it first.
- **Training path**: ground truth in, loss computed against the output. Mark every train-only row `security`, so the inference path reads alone at a glance.

Then a short bulleted list under the diagram, one bullet per component, carrying the layer counts, widths, tensor shapes and step counts. That list is the "detailed when expanded" half of this panel — it does the job the compact rows cannot.

Two hard limits, both measured:

- **Do not use a native `architecture` spec file here.** It accepts `sublabel`, `tag` and `meta.viewBox`, so it looks like the better choice, and it passes `finalize architecture` standalone with all four gates. As an explainer panel it **always** fails `explainer/browser-readability` at exactly 9.999873991935484px — a nominal 10px losing to a strict `<` comparison. That number does not move with `viewBox`, component count or `quality_profile`; it is an Archify defect, not something to tune around. Compact rows pass.
- **No back edge.** Compact rows carry no `viewBox`, so a gradient arrow routes outside the auto-sized canvas every time. State the gradient in the bulleted list instead, and say it is not drawn.

Keep the longest path to **four** components and labels to about 16 characters. A ten-node chain measured 2174px and failed at 5.0px; six nodes still failed the browser floor at 7.8px. Fold a trailing step into its predecessor rather than adding a column.

If the project fits no weights, say so on the panel and let the training rows show the optimization loop that runs instead.

## Panel 3: evaluation pipeline

`dataflow`: the outputs of panel 2 → the oracle, assay or metric computation → the final scores and the baselines compared against.

Define each metric in its node's `sublabel` — a reader who does not know the metric needs its meaning here. Thresholds, protocol details and the conditions under which a reported advantage does not hold go in cards.

## Measured constraints

Every number below came from a failing run. Respect them in the first draft and you will usually pass first time.

**Two legibility floors.** `finalize` enforces 6px; `--browser-check` renders in Chrome and enforces **10px**, measured at the width a panel gets inside the page, which is narrower than the same diagram standalone. Always run `--browser-check`.

**Canvas width drives everything.** A `dataflow` in an explainer needs **three stages** and `meta.viewBox` width near **820**. Four stages at 1000 passes the static gate and fails the browser gate. Shortening a sublabel does not rescue an over-wide canvas; dropping a stage does, and the stage becomes a card.

**Text budgets.** `dataflow` at `width: 140`: sublabel about 23 characters, label about 19. `architecture`: labels about 16. Overflow is a hard error naming the exact shortfall.

**Same-row flows need `route: "straight"`.** Port fan-out adds a 9px jog that fails the 16px interior-segment floor.

**A flow label includes its `classification`.** Both share one rect, so keep the label to one or two words and the classification short.

**Crowded labels lift with `labelDy: -34`.** Use `labelAt` when an offset keeps colliding, and keep node heights uniform (`height: 58`).

**A fan-in crowds labels, not lines.** No `labelDy` clears three flows converging on one node. Merge the sources where they mean the same thing, or take the exact `labelAt` the diagnostic suggests.

**Order rows to avoid crossings** rather than routing around them. Reordering two rows beats adding `via` points.

**`sources` are repository file paths** verified against `ARCHIFY_REPO_ROOT`, not URLs. A paper with no checked-out code gets none; put the citation in a `sublabel` or card as plain text.

**A card `dot`** must be `cyan`, `emerald`, `violet`, `amber`, `rose`, `orange` or `slate`.

## Delivery

One folder per run:

```sh
mkdir -p bc-out
node ~/.claude/skills/archify/bin/archify.mjs finalize explainer candidate.archify.md <slug>.html \
  --json --browser-check --out-dir bc-out
```

A non-zero exit is never success. Diagnostics name the component and a repair; fix the draft and rerun. Do not edit the delivered HTML. `rm -rf bc-out` between runs, or the run fails `viewer/evidence-path-conflict` on old evidence rather than on the draft.

Validate one panel alone while drafting — far cheaper than a whole-page finalize:

```sh
node ~/.claude/skills/archify/bin/archify.mjs finalize dataflow data-pipeline.dataflow.json data.html --json
```

A panel that passes standalone can still fail inside the page: the browser floor is measured at the narrower width a panel actually gets. Standalone is a fast filter, never the verdict.

To publish as a shareable artifact, prepend one line, because the artifact wrapper strips the root attributes the page's CSS keys off:

```sh
{ printf '<script>document.documentElement.setAttribute("data-style","swiss")</script>\n'; cat <slug>.html; } > publish.html
```

Publish `publish.html` with no capabilities. Archify's PNG, SVG and WebM export buttons work in the local file only; the viewer blocks downloads and warns on every publish — that warning is Archify's buttons and is expected.

## Reporting

Two or three lines: the conclusion, then the link. State the abstraction level, anything deliberately omitted, and any content dropped during layout repair.

Say "automated checks passed", not "visually reviewed", unless you looked at the rendered page. If the artifact is private, say so. For a proposal, keep confirmed decisions and open questions apart, and never present a plan as a result.
