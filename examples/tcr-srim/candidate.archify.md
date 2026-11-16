# What is the TCR-SRIM project? {#tcr-srim}

TCR-SRIM predicts whether a T-cell receptor binds a peptide, from three sequences and nothing else. The map it scores with is the map it shows as the explanation, so the two cannot drift apart.

- Inputs are CDR3a, CDR3b and the peptide. MHC is left out because most records do not carry it.[](source:arxiv)
- 274 solved structures train that map. None are needed to make a prediction.
- Explanations cost no extra step: the contact map is the classifier, not a reconstruction of it.
- Reported scores average the best 100 of 288 epitopes. Over all 288, every model sits near 0.55.

@answer scope="arXiv:2606.30902v1 · every number is the paper's · a node reading not specified is a fact the paper never gives"
@meta locale=en style=swiss quality_profile=showcase
@source arxiv title="Structure-Regularized Interpretable TCR-Epitope Prediction (arXiv:2606.30902v1)" url=https://arxiv.org/abs/2606.30902
@source code title="Tulane-Mettu-Landry-Lab/tcr-sr" url=https://github.com/Tulane-Mettu-Landry-Lab/tcr-sr

## What are the four parts of the project? {#components}

Every subcategory starts closed. Click one to see its facts; the badge counts what is still hidden.

@diagram tree id=four-components source=four-components.tree.json title="Task, Data, Modeling, Evaluation"

## Where does the data come from? {#data}

@diagram dataflow id=data-pipeline source=data-pipeline.dataflow.json title="Public databases to train, test and structure splits"

## What does the model compute? {#model}

@architecture id=model-pipeline title="Sequences to contact map to score"
| cdr3a | CDR3a | external |
| cdr3b | CDR3b | external |
| pep | Peptide | external |
| plm | Frozen PLM | backend |
| fef | Cross-attention | backend |
| proto | Contact prototype | backend |
| score | Binding score | backend |
| explain | Residue contact map | frontend |
| reg | Structure loss | security |
| ce | Cross-entropy | backend |
| cdr3a > plm | n x d | |
| cdr3b > plm | n x d | |
| pep > plm | n x d | |
| plm > fef | cached | emphasis |
| fef > proto | six maps | emphasis |
| proto > score | max of two | emphasis |
| proto > explain | read directly | emphasis |
| proto > reg | train only | security |
| score > ce | label | |
