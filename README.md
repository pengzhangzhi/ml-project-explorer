# ml-project-explorer

A Claude Code skill that turns an ML paper, repository or technical report into one page with three panels: how the **data** is prepared, how the **model** works, and how it is **evaluated**.

The page is static HTML. It is for reading.

## The three panels

| Panel | Diagram | Holds |
|---|---|---|
| Data pipeline | `dataflow` | raw sources → processing → train / validation / test, with real counts |
| Model pipeline | `architecture` | input → components → output, with the training path dimmed |
| Evaluation pipeline | `dataflow` | model outputs → scoring → final metrics and baselines |

**One model panel covers both training and inference.** The inference path is drawn solid; rows that exist only in training — the ground truth, the loss — are marked `security` so they render dimmed. A reader sees the inference path at a glance and the training path when they look for it, on one canvas instead of two.

**Concise at first glance, technically detailed when expanded.** A node label is three or four words. The layer counts, widths, tensor shapes and step counts go in the detail lines Archify reveals on hover and click, in a short bulleted list under the diagram, and in cards. Unreported facts are marked "Not specified" and never invented.

The task and problem definition live in the conclusion paragraph. There is no components tree, no results panel, no ablation table, no caveat section and no interactive layer: if a fact fits nowhere, it is not important enough for the page.

## Why Archify rather than hand-written HTML

Because a diagram that is wrong is worse than no diagram, and only a gate catches it. `finalize` measures what an eyeball approves: in this repo's own history it has caught text rendering at 2.1px, 5.0px and 7.8px against the floor, label routes colliding in a corridor, and connections routed outside the canvas — all in drafts that looked fine.

Run it with `--browser-check`. That renders the page in Chrome and raises the floor to 10px, measured at the width each panel actually gets inside the page, which is narrower than the same diagram standalone. A panel can clear every static check and still fail there; several in this repo's history did.

## Install

```sh
git clone https://github.com/pengzhangzhi/ml-project-explorer ~/.claude/skills/ml-project-explorer
```

Requires the `archify` skill. Then ask Claude Code to explain a paper, or invoke it directly:

```
/ml-project-explorer https://arxiv.org/abs/XXXX.XXXXX
```

## What is in SKILL.md

The three panels, then a section of layout constraints with real numbers in it: the two legibility floors, three `dataflow` stages at `viewBox` width near 820, four-component chains with labels around 16 characters, `route: "straight"` on same-row flows, `labelDy: -34` for crowded labels, and the card `dot` enum.

It also records one Archify defect worth knowing before you reach for the obvious solution: a **native** `architecture` spec file accepts `sublabel`, `tag` and `meta.viewBox`, passes `finalize architecture` standalone with all four gates, and then **always** fails as an explainer panel at exactly 9.999873991935484px — a nominal 10px losing to a strict `<` comparison, unmoved by viewBox, component count or quality profile. Use compact `@architecture` rows.

Every number in it was measured by a failing run, not guessed.

## Examples

`examples/partidesign` is the reference: a unified biomolecular co-design model (bioRxiv 10.64898/2026.11.03.763860). Three panels, all four gates, zero diagnostics.

`examples/superseded` holds pages built under earlier contracts, kept as layout references. See the note in that folder.

## License

MIT
