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


## Deliver

panel 1 — data pipeline, panel 2 — model pipelin panel 3 — evaluation pipelines.

## Read the source first

Paper: method, data, experiments, appendix. Repository: README, configs, data loaders, model definitions, training and eval scripts — prefer the implementation over the documentation and note where they disagree. 
## Panel 1: data pipeline

Create a visual flowchart showing: Raw data → processing steps → final training/validation/test data. Include actual datasets, filtering, transformations, splits, sample counts, and tensor representations when available.

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
