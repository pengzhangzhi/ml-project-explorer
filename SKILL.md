---
name: ml-project-explorer
description: Turn an ML paper, repository or project specification into one interactive Archify artifact with exactly five panels — the data pipeline, the model architecture, the training pipeline, the inference pipeline and the evaluation pipeline — that a reader can ask questions of and annotate. Use when asked to explain, map, summarize or visualize an ML paper, project or codebase, or when given an arXiv or bioRxiv link and asked what the project does.
license: MIT
---

# ML Project Explorer

One Archify explainer page, five panels, nothing else. Four are **pipelines** — something enters, something happens to it, something comes out — and one is the **architecture**, the only static panel, because what a network is made of is not a process.

Together they answer the five questions a reader has about an ML project: where the data came from, what the model is, how it was fitted, what it does when you run it, and how anyone knows it works.

**Keep the architecture and the inference panels apart.** They are the question most often conflated, and the distinction is the point: the architecture panel is what the network *is* — blocks, layer counts, widths, heads, where the weights live. The inference panel is what *happens* when a user asks for an output — building the input, the encode, the sampling loop or forward pass, decoding, post-processing, ranking. A layer count belongs in the first; a step count belongs in the second.

Read `~/.claude/skills/archify/SKILL.md` and `~/.claude/skills/archify/references/explainer-authoring.md` first. Archify does the rendering; never hand-write HTML or a second viewer.

The page is user-facing, so every word on it — the conclusion, the reasons, the panel titles, node labels, sublabels, tags and cards — is written for a reader, not for a log. If a `writing` skill is installed, read it before drafting and follow it. Either way: the conclusion's first sentence must stand alone, a term the reader may not use gets its meaning alongside it, every number keeps its unit and what it was measured on, and a fact stated in one place is not rephrased in another.

## What you deliver

A single published artifact with:

- the one-paragraph conclusion, up to four reasons, `@answer scope=...`, and `@source` lines (this is the document header, not a panel);
- **panel 1 — data pipeline**, a `dataflow`: where the data came from and how it was split;
- **panel 2 — model architecture**, an `architecture`: what the network is made of;
- **panel 3 — training pipeline**, a `lifecycle`: the loop that fitted it, and on what hardware;
- **panel 4 — inference pipeline**, a `lifecycle`: what happens when a user asks for an output;
- **panel 5 — evaluation pipeline**, a `dataflow`: outputs to numbers to baselines;
- an **ask-and-annotate layer**, so the reader can question any node and mark up the page.

No components tree, no results panel, no ablation tables, no caveat panels, no checklist. The task and problem definition live in the conclusion and the reasons, which is where a reader looks for them anyway. Every other fact belongs in a node `sublabel` or `tag`, a diagram `card`, or the `scope` line. If a fact fits nowhere, it is not important enough for the page.

**Always five panels, even when a stage is missing.** A project that trains nothing still gets a training panel — it shows the optimization loop that runs instead, and says plainly that no weights are updated. A project that releases no new network still gets an architecture panel, naming the frozen components it composes and whose weights they are. A project with no held-out evaluation still gets panel 5, showing what was measured instead. Deleting a panel hides the most interesting thing about such a project; naming the absence is the whole point. Never pad a panel with `not specified` nodes to fill the slot.

## Investigate the source first

For a paper, read the method, datasets, experiments and the appendix. For a repository, read the README, configs, data loaders, model definitions, training and eval scripts, and prefer the implementation over the documentation; note where they disagree. For a specification, separate decided from proposed.

Preserve exact names, counts, tensor shapes and terminology. Mark an absent fact `not specified` and never guess one. Distinguish what a paper reports, what you inferred from code, and what is only proposed.

## Panel 1: data pipeline

A `dataflow`: raw sources → filtering and labeling → train / validation / test. Put real counts and splits in sublabels. Give an absent fact a node of its own (`"Validation split"`, sublabel `"not specified"`) only when the omission changes how a reader reads the results; otherwise say it in a card.

Stop at the splits. Losses belong to panel 3 and metrics to panel 5.

## Panel 2: model architecture

Compact `@architecture` rows: input representation → encoders, blocks, branches → output heads. This is the one static panel. Put the layer counts, token and pair widths, head counts and latent dimensions here, in the node labels where they fit and in the prose line above the diagram where they do not.

- **No back edges.** Every one routes outside the auto-sized canvas, and compact `@architecture` rows cannot carry a `viewBox` to enlarge it. Three attempts at a loop-back arrow failed in a row. Recurrence belongs in panel 3 or 4, which is why those panels are `lifecycle`.
- **Keep the longest path to four nodes** and about 16 characters per label. A ten-node chain measured 2174px and failed the static floor at 5.0px; a six-node chain cleared that and still failed the browser floor at 7.8px. Branch at the output heads rather than extending the chain: a diamond spends height instead of width.
- Make the input node `external` rather than giving the input its own node — that saves one step of depth, which is a quarter of the budget.
- Name whose weights these are. A project that trains nothing composes released components, and the panel should say so (`"No new network / all weights are released"`).

## Panel 3: training pipeline

A `lifecycle` spec file. This is the loop: initialize → one step over a batch → the update → the condition that ends it. `lifecycle` is the only diagram type whose back edges are first-class — its own schema says *"transitions back to an earlier main-path state are drawn as loops above"* — so a training loop finally draws as a loop instead of a straight chain with a sentence apologizing for it.

- `schema_version` is **3**, not 1. Every other type uses 1; this one will fail validation with `schema/const` if you copy a header.
- `mainPath` is required and holds **at most 4 states**. Five measured 1489px against a 1395px desktop cap. Put the outcomes (`success`, `failure`) on branches off the decision rather than extending the main path; they are drawn below it and cost no width.
- State `type` is one of `start`, `active`, `waiting`, `decision`, `success`, `failure`, `neutral`, `external`. The loop condition is a `decision`; the branch that goes around again is a `failure` or `active` state with a transition back to an earlier main-path state.
- **Leave main-path transitions unlabelled.** A label widens the gap between the states it joins, and four labelled gaps alone pushed a passing diagram 355px over the cap. Label the branches off the decision (`"yes"`, `"no"`, `"next cycle"`) — those carry the real information — and put anything longer in a transition `note`.
- A state takes **three** authored lines: `label`, `sublabel` and `tag`. `tag` is *visible*, not hover-only, and is capped near 212px — about **48 characters**. Longer fails with `lifecycle/state-text-too-long` naming the exact shortfall.
- Use `tag` for the teaching line: what this step actually does to the data, or the one number that changes how it reads. This is the panel where a non-specialist learns the most, because `lifecycle` is the only type that renders a third line and the only pipeline type that feeds hover (below).

This is the panel for the training **hyperparameters and process**: optimizer and learning rate, batch size and how it was reached, the schedule, the hardware and how long it ran, what is frozen and what updates. Put the headline figures in the state lines and the full table in a card — a reader comparing two papers wants the optimizer and the device count, and a reader reproducing one wants the rest.

For a project that fits no weights, the states are the inference-time optimization loop, and the `start` state says so in its sublabel.

## Panel 4: inference pipeline

Also a `lifecycle`, for the same two reasons: a sampling loop, a diffusion or flow solver, a refinement cycle or an autoregressive decode is a **loop**, and `lifecycle` is the only type that draws one; and it carries hover, which a reader needs most on the panel describing what they would actually run.

This panel answers "what happens when I ask for an output" end to end: building the input, any encode of the context, the loop with its **step count** (not its layer count — that is panel 2), decoding, chemical or structural reconstruction, post-processing, filtering and ranking. Name what falls outside the measured cost, because papers routinely time only the GPU interval.

A model whose inference is a single forward pass with no loop still gets this panel: the states are the real stages a user's request passes through, and the absence of a loop is itself worth seeing next to a paper that needs 400 steps.

## Panel 5: evaluation pipeline

A `dataflow`: the outputs of panel 4 → the oracle or assay that scores them → the metrics → the baselines they are compared against. This is where the numbers live, and where the baselines live now that there is no components tree.

Evaluation consumes inference, so say what was run to produce the candidates being scored — how many, under what settings, and whether the scoring model is independent of the generating one.

Name each metric as a node and define it in the node's `sublabel` — a reader who does not know Uno's C-index or ipSAE needs its meaning here, not in a glossary. Put the thresholds and the caveats that change how a number reads into cards.

Stop at the comparison. A conclusion drawn from the numbers belongs in the page's conclusion paragraph.

## Which panels the reader can hover and focus

Not every diagram type exposes its nodes. `render-tree`, `render-architecture` and `render-lifecycle` call `focusNodeAttrs`; `render-dataflow` and `render-workflow` do not.

- **Panels 2, 3 and 4** emit focusable nodes with `data-node-label`, `data-node-sublabel`, `data-node-tag` and an SVG `<title>`, so hovering a node shows `label · sublabel · context · tag` and the Semantic Passport opens on click. The two `lifecycle` panels are the richest surfaces on the page, because only `lifecycle` renders and exposes a third authored line.
- **Panels 1 and 5** emit `data-node-label=""` — the attribute is present but empty. Their nodes can be annotated but carry no hover detail, so a `dataflow` stage whose meaning needs a sentence gets that sentence in a `card`, named so the reader can tie the two together.

Never plan teaching detail into a `dataflow` node expecting it to be reachable on hover. It is not.

## Archify constraints that cause most failures

Learned from real finalize runs. Respect them in the first draft and you will usually pass on the first try.

**There are two legibility floors, and the browser one is stricter.** `finalize` without `--browser-check` enforces a static **6px** minimum. `--browser-check` renders the page in Chrome and enforces **10px**, measured at the width the panel actually gets inside the explainer — which is narrower than the same diagram standalone. A panel can pass every static gate and still fail in the browser: the lifecycle probe that passed standalone rendered at 8.7px once it was one panel of four. Always run `--browser-check`, and never report a page as checked without it.

**Legibility sets the canvas size.** Sublabel text renders at a fixed size and shrinks with the page scale, so a wide diagram fails no matter how short the text. Inside a four-panel explainer, keep a `dataflow` at **three stages** with `meta.viewBox` width near **820**. Four stages at 1000 passes the static gate and fails the browser gate — that cost three repair rounds in a row, once per dataflow panel. Shortening a sublabel does not rescue an over-wide canvas; dropping a stage does. A stage you drop becomes a `card`, which loses nothing: a card holds more words than four nodes ever could.

**Node text budget.** At `width: 140` keep `dataflow` sublabels to about **23 characters** and labels to about 19. A `lifecycle` tag gets about 48. Either overflow is a hard error naming the exact shortfall.

**Same-row flows need `route: "straight"`.** When two nodes share a row across adjacent stages, port fan-out introduces a 9px jog that fails the 16px interior-segment floor. A straight route removes it.

**A flow label includes its `classification`.** Both strings share one rect, so a long classification widens the label and collides with nodes. Keep the label to one or two words and the classification short, or move the detail into a card.

**Lift crowded labels with `labelDy`.** About `-34` clears the row for a label wider than the gap between two nodes. Use `labelAt` when an offset keeps colliding. Keep node heights uniform (`height: 58`) so rows align.

**Avoid crossings by ordering rows, not by routing.** Assign rows so flows run straight or fan out once. Reordering two rows beats adding `via` points.

**A fan-in crowds labels, not lines.** Two or three flows converging on one node put their labels in the same corridor, and no `labelDy` clears it — every offset collides with either the other label, the vertical segment, or a node. Merge the sources into one node where they mean the same thing, and otherwise take the exact `labelAt` the diagnostic suggests: it computes a free position, so it lands first time where guessing does not.

**`sources` are repository file paths**, verified against `ARCHIFY_REPO_ROOT` — not URLs. A paper with no checked-out code gets none, and a cited work cannot become a clickable pin. Put the citation in a `sublabel` or a card as plain text instead.

## Delivery

Give each run its own folder, then:

```sh
mkdir -p bc-out
node ~/.claude/skills/archify/bin/archify.mjs finalize explainer candidate.archify.md <slug>.html \
  --json --browser-check --out-dir bc-out
```

A non-zero exit is never success. Diagnostics name the line, the component and a repair; fix the draft and rerun the same command. Do not edit the delivered HTML.

Two things that waste a round each: a `card` `dot` must be one of `cyan`, `emerald`, `violet`, `amber`, `rose`, `orange`, `slate` — no other colour name validates. And `--out-dir` must be a directory nothing has written to, or the run fails `viewer/evidence-path-conflict` on a previous run's evidence rather than on anything in the draft; `rm -rf` it between runs.

While drafting a single panel, validate it alone — it is far cheaper than a whole-page finalize:

```sh
node ~/.claude/skills/archify/bin/archify.mjs finalize lifecycle train.lifecycle.json train.html --json
```

## The ask-and-annotate layer

A finished page is a draft, not a result. Ship it so the reader can interrogate it and the author can mark it up, then revise from what they say.

Build the publish file by injecting `ask-overlay.html` before the delivered page's last `</body></html>`, and prepend the style line, because the artifact wrapper strips the root attributes the page's CSS keys off:

```sh
python3 -I - <<'EOF'
src = open("<slug>.html", encoding="utf-8").read()
overlay = open("ask-overlay.html", encoding="utf-8").read()
m = "</body></html>"; i = src.rindex(m)
open("publish.html", "w", encoding="utf-8").write(
    '<script>document.documentElement.setAttribute("data-style","swiss")</script>\n'
    + src[:i] + "\n" + overlay + "\n" + src[i:])
EOF
```

The overlay lives beside this file. Never edit the delivered HTML to add it. Publish `publish.html` with:

```
capabilities: {
  "sample": {},
  "comments": {"composer_only": true},
  "user": {},
  "db": {"rules": [{"path": "questions", "read": "admin", "write": "self"},
                   {"path": "questions/{self}", "read": "interact", "write": "interact"}]}
}
```

What each one buys, and the rules that bite:

- `sample` lets the page answer questions about itself. The overlay builds its grounding text from the page's own DOM at load — conclusion, reasons, and every node with its sublabel and ancestors — and instructs that every number must appear on the page and that `not specified` means the source is silent. Calls spend the **viewer's** Claude usage and the first one asks their permission.
- `comments: {composer_only: true}` is the annotate loop. The page takes over a node click and opens claude.ai's own comment composer anchored to that node; the shell does all the writing, so no consent is asked and the page never holds a credential. **Send to Claude** on the thread reaches the session that built the page — read it with `ArtifactComments`, change the spec files, rerun `finalize`, republish, reply saying what changed, then resolve the thread.
- `db` logs each viewer's questions to a document only the page's editors can read, so one reader never sees what another did not understand. Read it back with `ArtifactData` and rewrite the sublabels that keep generating questions. **A db rule's `read` may not outrank its `write`**, or the publish is refused 422.
- Archify's PNG, SVG and WebM export buttons work in the local file only; the artifact viewer blocks downloads and warns about it on every publish. Ignore that warning — it is Archify's buttons, not the overlay.

Then annotate the page yourself before handing over the link. It is the cheapest review there is: one note on a node whose provenance looked thin found a real defect on the first try — a node that had silently merged two different data sources into one label.

## Reporting

Two or three lines: the conclusion, then the link. State the abstraction level, anything deliberately omitted, and any requested content dropped during layout repair.

Say "automated checks passed" and not "visually reviewed" unless you actually looked at the rendered page. Name any panel whose stage the source does not really have, and say what you put there instead. For a proposal, keep confirmed decisions and open questions apart, and never present a plan as a result.

If the artifact is private, say so — the reader cannot open the link until it is shared.
