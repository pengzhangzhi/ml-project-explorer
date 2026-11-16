# Superseded examples

These were built under earlier versions of the contract. Their specs are valid
Archify and still pass their own gates; the panel structure is what changed.

- `germinal-multi` — four panels (data, training, inference, evaluation), and
  later five. An antibody design paper that trains nothing, so its training
  panel held an inference-time optimization loop. Useful as a `lifecycle`
  reference, which the current three-panel contract no longer uses.
- `tcr-srim`, `ladon` — the original three panels, built around a
  `tree` of Task/Data/Modeling/Evaluation. Useful as `dataflow` layout
  references. Neither was ever run through `--browser-check`, and both would
  fail it: four stages at `viewBox` width 1000 clears the static 6px floor and
  misses the browser's 10px floor.

Do not copy their structure for a new page.
