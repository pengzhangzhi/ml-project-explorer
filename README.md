# ml-project-explorer

A Claude Code skill that turns an ML paper, repository or technical report into one interactive page with exactly three panels: a drill-down map of **Task, Data, Modeling and Evaluation**, the **data pipeline**, and the **model pipeline**.

It renders through [Archify](https://github.com/tt-a1i/archify), so the output is a validated, self-contained HTML page with a real diagram viewer — click-to-expand branches, node focus, deep links, keyboard navigation — not a static image.

## What you get

Three panels and nothing else. Depth lives inside the diagrams rather than in extra sections, so the page reads in thirty seconds and still answers a specialist's questions when they click into it.

| Panel | Diagram | Holds |
|---|---|---|
| The four parts | `tree` | Task, Data, Modeling, Evaluation, each opening down to single facts |
| Where the data comes from | `dataflow` | sources → quantification → pools → held-out splits, with real counts |
| What the model computes | `architecture` | inputs → encoders and heads → outputs, with shapes where the source gives them |

Two rules do most of the work:

- **A fact the source never states reads `not specified`.** It is never guessed, and never quietly dropped. On the Ladon example, seven nodes say so — which is itself the most useful thing the page tells you about that source.
- **Paper-reported, code-inferred and merely-proposed facts stay distinguishable.** A plan is not presented as a result.

## Install

A skill is installed by putting its directory in `~/.claude/skills/`, named to match the skill's `name:` field — so cloning *is* installing:

```sh
git clone https://github.com/pengzhangzhi/ml-project-explorer.git ~/.claude/skills/ml-project-explorer
```

Archify is a separate skill and must be installed too, at `~/.claude/skills/archify/`. This skill reads Archify's own `SKILL.md` and `references/explainer-authoring.md` before drafting, and calls its `finalize` command to render.

Then ask for it by name, or just point Claude at a paper:

```
Use the ml-project-explorer skill on https://arxiv.org/abs/2606.30902
```

## Examples

`examples/` holds the input specs for two real runs — the Archify source, not the rendered HTML, which is 1.7 MB a page and regenerates in one command:

```sh
cd examples/ladon
node ~/.claude/skills/archify/bin/archify.mjs finalize explainer candidate.archify.md ladon.html --json
```

- **`tcr-srim/`** — a structure-regularized TCR-epitope binding predictor, from an arXiv preprint.
- **`ladon/`** — a 3B-parameter bulk-transcriptome foundation model, from a company technical report. Thinner source, and the page shows it.

These double as the test suite: if an Archify release changes the layout validators, re-rendering both specs is how you find out.

## Why the constraints section exists

Most of `SKILL.md` is the three panels. The rest is a section of layout constraints with real numbers in it — the 6px text floor, the 16px segment floor, four stages maximum, `viewBox` width near 1000, `route: "straight"` on same-row flows.

Those came from watching Archify's validators reject real drafts. The first build took seven repair rounds. The second, written with the constraints in hand, took three. That gap is the whole value of the file.

## Dependencies

- [Claude Code](https://claude.com/claude-code)
- [Archify](https://github.com/tt-a1i/archify) (MIT / Apache-2.0)

Optionally a `writing` skill at `~/.claude/skills/writing/`. If you have one, this skill reads it before drafting the page's prose; if you don't, the same guidance is inlined in brief.

## License

MIT
