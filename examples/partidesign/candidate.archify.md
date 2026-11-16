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

## What is the model made of? {#architecture}

Three networks share one token width of 768 and 12 attention heads. The autoencoder is 12 Transformer layers each way, with a pair width of 256. The flow denoiser is 14 layers at those widths, plus a 256-wide condition track. The ligand graph decoder is smaller: three message-passing layers, node width 256 and edge width 128.

Iterative pair updates and triangular multiplication are switched off here. That is where the efficiency over an AlphaFold 3-like stack comes from.

@architecture id=model-pipeline title="One token type, one latent, and three networks that share it"
| enc | Encoder x12 | external |
| den | Denoiser x14 | backend |
| dec | Decoder x12 | backend |
| poly | Polymer atoms | frontend |
| lig | Ligand graph | frontend |
| enc > den | latent, 8 dims | emphasis |
| den > dec | centers, latents | emphasis |
| dec > poly | 91 atom slots | |
| dec > lig | bonds, charges | |

## How was it trained? {#training}

Two networks are trained in sequence, and the first is frozen before the second begins. Hover a state for the detail line.

@diagram lifecycle id=training-loop source=training.lifecycle.json title="The autoencoder learns a local latent; the flow matcher learns to generate it"

## What happens when you ask for a design? {#inference}

@diagram lifecycle id=inference-loop source=inference.lifecycle.json title="400 flow steps, then one decode, then a ranking pass"

## How is a design judged? {#evaluation}

@diagram dataflow id=eval-pipeline source=eval-pipeline.dataflow.json title="Refold the design, apply the benchmark's thresholds, divide into one GPU-day"
