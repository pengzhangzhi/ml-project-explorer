---
name: ml-project-explorer
description: Turn an ML paper, repository or project specification into one Archify explainer page with three panels — the data pipeline, the model pipeline covering both training and inference, and the evaluation pipeline. Use when asked to explain, map, summarize or visualize an ML paper, project or codebase, or when given a paper link and asked what the project does.
license: MIT
---

# ML Project Explorer

One Archify explainer page, three panels. A reader should learn what the project does, how its data is prepared, how its model works, and how it is trained and evaluated.

Read `~/.claude/skills/archify/SKILL.md` and `~/.claude/skills/archify/references/explainer-authoring.md` first. Archify renders; never hand-write HTML.

**Concise at first glance, technically detailed when expanded.** A node `label` is three or four words — what a reader takes in without acting. Shapes, counts, rates and layer sizes go in `sublabel` and `tag`, which Archify reveals on hover and click, in a short bulleted list under a diagram, or in a `card`. Keep every exact name, number and shape the source gives; cut the explanation around them.

Mark an unreported fact **Not specified**. Never invent one, and never smooth over a gap with a plausible default.

If a `writing` skill is installed, read it before drafting.

## Deliver

- a header: one-paragraph conclusion, up to four reasons, `@answer scope=...`, `@source` lines;
- **panel 1 — data pipeline**, a `dataflow`: raw sources → processing → train / validation / test;
- **panel 2 — model pipeline**, compact `@architecture` rows: input → components → output, training and inference on one canvas;
- **panel 3 — evaluation pipeline**, a `dataflow`: model outputs → scoring → final metrics.

Nothing else. No components tree, no results panel, no ablation tables, no caveat section, no interactive layer. The task and problem definition belong in the conclusion. A fact that fits no node, card or the `scope` line is not important enough for the page.

## Read the source first

Paper: method, data, experiments, appendix. Repository: README, configs, data loaders, model definitions, training and eval scripts — prefer the implementation over the documentation and note where they disagree. Specification: separate decided from proposed.

Say which claims you took from the source, which you inferred from code, and which are only proposed.

## Panel 1: data pipeline

Raw sources → cleaning, filtering, labeling, augmentation, tokenizing or cropping → the final splits with their real sizes. Stop at the splits: losses belong to panel 2, metrics to panel 3.

State **how train and test were separated** — at random, by group, by time, by some identity key — and what that leaves shared. A split that controls less than a reader assumes is a fact that belongs beside the results, not in a footnote.

Give an absent fact its own node (`"Validation split"`, sublabel `"Not specified"`) when the omission changes how the results read; otherwise put it in a card.

## Panel 2: model pipeline

One canvas, both paths.

- **Spine**: input representation → encoders, blocks, branches → output. Mark the inference path `emphasis` so a reader sees it first.
- **Inference** is whatever the output becomes: a prediction or class for a discriminative model, a sampling or decoding loop with its step count for a generative one, an action for a policy. Name which it is.
- **Training** adds the ground truth and the loss computed against the output. Mark every train-only row `security` so it renders dimmed and the inference path reads alone at a glance.

Then a few bullets under the diagram, one per component, holding the layer counts, widths, tensor shapes and step counts. That list is this panel's expanded half; the rows themselves stay short.

Name what is frozen, what is pretrained elsewhere, and whose weights they are. A project that trains nothing still gets this panel: the training rows show the optimization loop that runs instead, and the page says no weights are updated.

## Panel 3: evaluation pipeline

The outputs of panel 2 → the scoring procedure → the final numbers and the baselines compared against.

Define each metric in its node's `sublabel`; a reader who does not know the metric needs its meaning there. Put thresholds, protocol details, and the conditions under which a reported advantage does not hold into cards. If the headline number depends on a budget, a draw count or a tuned threshold, say so next to it.

## Archify constraints

Each of these costs a repair round if ignored.

**Run `--browser-check`.** Plain `finalize` enforces a lower text floor than the browser gate, and the browser gate measures each panel at the narrower width it gets inside the page. A panel can pass standalone and fail in the page.

**Canvas width sets text size.** Keep a `dataflow` to about three stages and its `meta.viewBox` width under roughly 850. Shortening a sublabel does not rescue an over-wide canvas — dropping a stage does, and that stage becomes a card. Likewise keep an `@architecture` chain to about four components with short labels; fold a trailing step into its predecessor rather than adding a column.

**Use compact `@architecture` rows, not a native `architecture` spec file.** The native spec takes `sublabel`, `tag` and `meta.viewBox` and passes standalone, so it looks like the better choice, but as an explainer panel it always fails the browser text floor by a rounding margin that no setting moves. This is an Archify defect, not something to tune.

**No back edges in `@architecture`.** Compact rows have no `viewBox`, so a gradient or recurrence arrow routes off the canvas every time. State it in the bullets and say it is not drawn.

**`dataflow` labels and routing.** Same-row flows across adjacent stages need `route: "straight"`. A flow's `label` and `classification` share one box, so keep both short. Lift a crowded label with `labelDy: -34`, and take the exact `labelAt` a diagnostic suggests rather than guessing. Avoid crossings by reordering rows, not by adding `via` points. Keep node heights uniform.

**`sources` are repository file paths** verified against `ARCHIFY_REPO_ROOT`, not URLs. A paper with no checked-out code gets none; put citations in a `sublabel` or card as plain text.

**A card `dot`** must be one of `cyan`, `emerald`, `violet`, `amber`, `rose`, `orange`, `slate`.

## Build and publish

One folder per run:

```sh
mkdir -p bc-out
node ~/.claude/skills/archify/bin/archify.mjs finalize explainer candidate.archify.md <slug>.html \
  --json --browser-check --out-dir bc-out
```

A non-zero exit is never success. Diagnostics name the component and a repair; fix the spec and rerun. Never edit the delivered HTML. Clear `bc-out` between runs or the run fails on stale evidence rather than on the draft.

Validating one panel alone is a fast filter while drafting, but never the verdict — the browser floor only applies in the page.

To publish as a shareable artifact, prepend one line, because the artifact wrapper strips the root attributes the page's CSS keys off:

```sh
{ printf '<script>document.documentElement.setAttribute("data-style","swiss")</script>\n'; cat <slug>.html; } > publish.html
```

Publish `publish.html` with no capabilities. The viewer blocks downloads and warns about Archify's own export buttons on every publish; that warning is expected.

## Reporting

Two or three lines: the conclusion, then the link. Name anything you left out, and any content dropped during layout repair.

Say "automated checks passed", not "visually reviewed", unless you actually looked at the rendered page. If the artifact is private, say so.
