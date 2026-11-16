# Superseded examples

These two were built under the earlier three-panel contract: a `tree` of Task,
Data, Modeling and Evaluation, plus a data pipeline and a model pipeline. The
skill now delivers four pipelines instead, so `four-components.tree.json` in
these folders has no counterpart in the current `SKILL.md`.

They are kept because the data-pipeline specs are still valid worked examples
of `dataflow` layout, and because the trees record how much detail the old
hierarchy had to carry. Do not copy their structure for a new page.

One thing they demonstrate by omission: neither was ever run through
`--browser-check`. Both would fail it. Four stages at `viewBox` width 1000
clears the static 6px floor and misses the browser's 10px floor.
