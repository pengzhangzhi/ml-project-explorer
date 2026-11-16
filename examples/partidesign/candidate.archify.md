# What is the PartiDesign project? {#partidesign}

PartiDesign is one generative model that designs a protein, RNA, DNA or small molecule against a target made of any of those same four things. It generates the shape and the identity together — the sequence or chemical formula is not threaded onto a finished backbone afterwards.

- Everything becomes one kind of token. A polymer residue is a token; each ligand heavy atom is a token. Each carries a 3D center and an eight-dimensional latent.
- The headline claim is throughput, not accuracy: the most successful designs per GPU-day. That budget rewards a cheap generator as much as an accurate one.
- Give PXDesign eight sequence redesigns per backbone and it beats PartiDesign's best setting on successful-backbone rate, reversing the single-draw result.
- Trained on 128 Huawei Ascend 910B accelerators for about a month. Training excludes validation structures by PDB ID only, with no homology or ligand-scaffold split.

@answer scope="bioRxiv 10.64898/2026.11.03.763860, CC-BY · every number is the paper's · a node reading Not specified is a fact the paper never gives"
@meta locale=en style=swiss quality_profile=showcase
@source paper title="PartiDesign: A structure-sequence co-design paradigm for practical all-to-all biomolecules design (bioRxiv, 2026)" url=https://doi.org/10.64898/2026.11.03.763860
@source code title="MegaGatlingPea/PartiDesign-official" url=https://github.com/MegaGatlingPea/PartiDesign-official
@source server title="PartiDesign web server, Zhejiang University" url=https://cadd.zju.edu.cn/partidesign/

## How is the data prepared? {#data}

@diagram dataflow id=data-pipeline source=data-pipeline.dataflow.json title="Eleven sources, reweighted per modality, cropped to 512 tokens"

## How does the model work? {#model}

Solid is the inference path; the dimmed rows exist only in training.

@architecture id=model-pipeline title="One token type in, structure and sequence out; the training path is dimmed"
| in | Tokens | external |
| enc | VAE encoder x12 | backend |
| den | Flow denoiser x14 | backend |
| dec | Decoded designs | frontend |
| gt | Clean endpoint | external |
| loss | Flow loss | security |
| in > enc | features | |
| enc > den | z, 8 dims | emphasis |
| den > dec | centers + z | emphasis |
| gt > loss | target | security |
| den > loss | velocity | security |

- **Tokens**: one per polymer residue, one per ligand heavy atom, each a 3D center plus an eight-dimensional latent.
- **VAE encoder**: 12 layers, token width 768, 12 heads, pair width 256. Frozen before flow training starts.
- **Flow denoiser**: 14 layers at those widths. 400 steps at inference, with separate noise times for centers and latents.
- **Decoder**: 12 layers writing atoms into 91 canonical slots, plus a 3-layer graph head for ligand bonds and charges.
- **In training only**: the frozen encoder's posterior sample is the clean endpoint, and the time-weighted loss updates the denoiser alone. The gradient is not drawn: compact architecture rows cannot carry a back edge.

## How is it evaluated? {#evaluation}

@diagram dataflow id=eval-pipeline source=eval-pipeline.dataflow.json title="Refold the design, apply the benchmark's thresholds, divide into one GPU-day"
