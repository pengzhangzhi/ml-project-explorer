# ml-project-explorer

A Claude Code skill that turns an ML paper, repository or technical report into one interactive page with exactly four panels — the **data pipeline**, the **training pipeline**, the **inference pipeline** and the **evaluation pipeline** — that a reader can ask questions of and mark up.

## Why four pipelines

Every panel is a pipeline: something enters, something happens to it, something comes out. Together they answer the only four questions a reader has about an ML project — where the data came from, how the thing was fitted, what it computes at inference, and how anyone knows it works.

| Panel | Diagram | Holds |
|---|---|---|
| Data pipeline | `dataflow` | sources, filtering, the splits and their real counts |
| Training pipeline | `lifecycle` | the loop that fitted the model, drawn as a loop |
| Inference pipeline | `architecture` | what one forward pass computes |
| Evaluation pipeline | `dataflow` | held-out data, the metrics defined, the baselines |

The task and problem definition live in the conclusion paragraph, where a reader looks for them anyway. Everything else goes in a node's detail lines or a card. There is no results panel, no ablation table and no caveat section: if a fact fits nowhere, it is not important enough for the page.

**Always four panels, even when a stage is missing.** A project that trains nothing still gets a training panel — it shows the optimization loop that runs at inference and says plainly that no weights are updated. Deleting the panel would hide the most interesting thing about such a project.

## The interactive layer

A finished page is a draft, not a result. Each page ships with two loops:

- **Ask** — the page answers questions about itself. It builds its grounding text from its own rendered content at load, and is instructed that every number it states must appear on the page, and that a node reading `not specified` means the source is silent rather than something to fill in.
- **Annotate** — clicking a node opens claude.ai's own comment composer anchored to that node. Send the thread to Claude and the session that built the page reads it, edits the spec files, re-runs the gate and republishes.

The second loop is the one that earns its keep. The first note left on the first page built this way found a real defect: a node whose label had silently merged two different data sources into one phrase.

## Why Archify rather than hand-written HTML

Because a diagram that is wrong is worse than no diagram, and only a gate catches it. `finalize` measures what an eyeball approves: it has caught text rendering at 2.1px and 5.0px against a 6px floor, label routes colliding in a corridor, and connections routed outside the canvas — all in drafts that looked fine.

Run it with `--browser-check`. That renders the page in Chrome and raises the floor to 10px, measured at the width each panel actually gets inside the page, which is narrower than the same diagram standalone. A panel can clear every static check and still fail there; one in this repo's own history did.

## Install

```sh
git clone https://github.com/pengzhangzhi/ml-project-explorer ~/.claude/skills/ml-project-explorer
```

Requires the `archify` skill. Then ask Claude Code to explain a paper, or invoke it directly:

```
/ml-project-explorer https://arxiv.org/abs/XXXX.XXXXX
```

## What is in SKILL.md

The four panels, then a section of layout constraints with real numbers in it: the two legibility floors, three `dataflow` stages at `viewBox` width near 820, four-node chains in `architecture`, `lifecycle` at `schema_version: 3` with at most four main-path states and a 48-character visible `tag`, and which diagram types expose their nodes to hover at all (`tree`, `architecture` and `lifecycle` do; `dataflow` and `workflow` do not).

Every number in it was measured by a failing run, not guessed.

## Examples

`examples/germinal-multi` is the four-panel reference: an antibody design paper that trains nothing, which is the case the structure has to bend to honestly. It passes all four gates, browser-check included.

`examples/legacy-three-panel` holds two pages built under the earlier contract, kept as `dataflow` layout references. Their structure is superseded; see the note in that folder.

## License

MIT
