# What is the PartiDesign project? {#partidesign}

PartiDesign is one generative model that designs a protein, RNA, DNA or small molecule against a target made of any of those same four things. It generates the shape and the identity together — the sequence or chemical formula is not threaded onto a finished backbone afterwards.

- Everything becomes one kind of token. A polymer residue is a token; each ligand heavy atom is a token. Every token carries a 3D center and an eight-dimensional latent, so one model handles all four modalities.
- The headline claim is throughput, not accuracy: the most successful designs per GPU-day under the paper's own compute budget. The budget rewards a cheap generator as much as an accurate one.
- Give PXDesign eight sequence redesigns per backbone and it beats PartiDesign's best setting on successful-backbone rate, reversing the single-draw result.
- Trained on 128 Huawei Ascend 910B accelerators for about a month. Training excludes validation structures by PDB ID only, with no homology or ligand-scaffold split.

@answer scope="bioRxiv 10.64898/2026.11.03.763860, CC-BY · every number is the paper's · a node reading not specified is a fact the paper never gives"
@meta locale=en style=swiss quality_profile=showcase
@source paper title="PartiDesign: A structure-sequence co-design paradigm for practical all-to-all biomolecules design (bioRxiv, 2026)" url=https://doi.org/10.64898/2026.11.03.763860
@source code title="MegaGatlingPea/PartiDesign-official" url=https://github.com/MegaGatlingPea/PartiDesign-official
@source server title="PartiDesign web server, Zhejiang University" url=https://cadd.zju.edu.cn/partidesign/

## What was it trained on? {#data}

@diagram dataflow id=data-pipeline source=data-pipeline.dataflow.json title="Eleven sources, reweighted per modality, cropped to 512 tokens"

## How was it trained? {#training}

Two networks are trained in sequence, and the first is frozen before the second begins. Hover a state for the detail line.

@diagram lifecycle id=training-loop source=training.lifecycle.json title="The autoencoder learns a local latent; the flow matcher learns to generate it"

## What happens when you ask for a design? {#model}

The solver runs 400 flow steps, moving token centers and token latents at once. Nothing is predicted and then redesigned: one pass produces coordinates and identities together.

@architecture id=model-pipeline title="Noise and context in, structure and sequence out, ranked before you see it"
| ctx | Target, hotspots | external |
| noise | Gaussian noise | external |
| flow | Flow solver | backend |
| dec | Atoms and identities | backend |
| rank | PartiScore rank | frontend |
| ctx > flow | context | emphasis |
| noise > flow | centers, latents | |
| flow > dec | 400 steps | emphasis |
| dec > rank | molecules | emphasis |

## How is a design judged? {#evaluation}

@diagram dataflow id=eval-pipeline source=eval-pipeline.dataflow.json title="Refold the design, apply the benchmark's thresholds, divide into one GPU-day"
