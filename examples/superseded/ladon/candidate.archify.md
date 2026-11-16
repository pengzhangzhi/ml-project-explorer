# What is the Ladon project? {#ladon}

Ladon is a 3-billion-parameter encoder for bulk RNA-seq, and it never learns the clinical task. It is trained on expression, then frozen, and a small head is fitted per outcome on top of its embeddings.

- Input is one bulk RNA-seq profile, plus clinical text where the cohort records it.[](source:report)
- Pretraining reconstructs masked isoform-level sequences. Mid-training then predicts stage, age, grade and mutations, and those heads are discarded.
- Eight industry trial arms are each scored without ever being fitted on. On OAK, 699 patients, the Uno C-index is 0.61.
- Most reported numbers appear only in figures, and the architecture below the parameter count is not given.

@answer scope="Ladon Technical Report, Blank Bio, 12 November 2026 · every number is the report's · a node reading not specified is a fact the report never gives"
@meta locale=en style=swiss quality_profile=showcase
@source report title="Ladon Technical Report (Blank Bio, 2026)" url=https://www.blank.bio/ladon-technical-report
@source blog title="Introducing Ladon: An RNA foundation model for predicting patient outcomes" url=https://www.blank.bio/introducing-ladon

## What are the four parts of the project? {#components}

Every subcategory starts closed, and a card with a badge opens further. Click to reach the facts.

@diagram tree id=four-components source=four-components.tree.json title="Task, Data, Modeling, Evaluation"

## Where does the data come from? {#data}

@diagram dataflow id=data-pipeline source=data-pipeline.dataflow.json title="Expression archives and single-cell runs to the held-out splits"

## What does the model compute? {#model}

@architecture id=model-pipeline title="One profile to a frozen embedding to a per-task head"
| expr | Gene-level TPM | external |
| cov | Clinical text | external |
| enc | Ladon encoder | backend |
| dec | Decoder | security |
| pool | Mean of latents | backend |
| bmr | BMRetriever-7B | backend |
| pca | PCA | backend |
| cox | Ridge Cox | backend |
| tab | TabICL v2 | backend |
| risk | Risk rank | frontend |
| resp | Response score | frontend |
| expr > enc | 20,250 genes | |
| cov > bmr | free text | |
| enc > dec | train only | security |
| enc > pool | latents | emphasis |
| pool > pca | 64 or 128 PCs | emphasis |
| bmr > cox | covariates | |
| pca > cox | features | emphasis |
| pca > tab | features | emphasis |
| cox > risk | C-index | emphasis |
| tab > resp | AUROC | emphasis |
