# What is the Germinal Multi project? {#germinal-multi}

Germinal Multi designs one antibody against several versions of the same target at once. Nothing is trained: frozen structure predictors score a single candidate sequence against every variant, and their gradients are combined to move it.

- Each campaign uses two antigens, not many. For EGFR that means wild type plus one composite antigen carrying all six resistance substitutions.
- A rescue mode starts from an existing antibody and biases every CDR position back toward its parent, to repair a drug rather than replace it.
- Designs were screened in the tens: 60, 57 and 60. X51 neutralized the engineered escape variant at 6.3 ng/mL and its parent at 2.1 ng/mL.
- Binding does not imply neutralization. X58 bound both XFG receptor-binding domains and blocked only one.

@answer scope="bioRxiv 10.64898/2026.11.10.765734v1, CC-BY · every number is the paper's · a node reading not specified is a fact the paper never gives"
@meta locale=en style=swiss quality_profile=showcase
@source paper title="Antibody design across observed and forecasted antigenic evolution (bioRxiv, 2026)" url=https://www.biorxiv.org/content/10.64898/2026.11.10.765734v1
@source code title="twidatalla/germinal-multi" url=https://github.com/twidatalla/germinal-multi

## Where do the design targets come from? {#data}

@diagram dataflow id=data-pipeline source=data-pipeline.dataflow.json title="Structures, escape maps and sequence counts to two antigens per campaign"

## What loop produces a candidate? {#training}

No weights are updated anywhere in this pipeline, so the loop below optimizes the antibody's own sequence instead of a model. Hover a state for the detail line.

@diagram lifecycle id=training-loop source=training.lifecycle.json title="One sequence, refolded against every target, kept only when the worst target improves"

## What does one scoring pass compute? {#model}

@architecture id=model-pipeline title="A single shared sequence, folded and scored against every target in turn"
| ag | Targets and templates | external |
| logits | Shared CDR logits | backend |
| afm | AF2-Multimer | backend |
| worst | Worst target's ipSAE | backend |
| out | Ranked candidates | frontend |
| ag > afm | one per target | |
| logits > afm | one sequence | emphasis |
| afm > worst | per-target score | emphasis |
| worst > out | 0.55 floor | emphasis |

## How is a design judged? {#evaluation}

@diagram dataflow id=eval-pipeline source=eval-pipeline.dataflow.json title="Two assay tracks, and what each one can and cannot prove"
