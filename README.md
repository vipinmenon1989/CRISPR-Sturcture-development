# CRISPR Structure Development Pipeline

An integrated computational pipeline for analysing sgRNA on-target activity and off-target risk through a combined sequence–structure framework applied to SpCas9 and Cas12f systems.

## Overview

This pipeline connects three distinct analytical layers:

1. **Sequence preparation** — curate, filter, and score sgRNA sequences (PAM validation, Azimuth/SSC scoring, stratified 150-guide sampling)
2. **3-D structure modelling** — generate and process PDB models of sgRNA–Cas9 complexes via RNAnneal; extract per-residue B-factor entropy across scaffold stem-loop regions
3. **Integrated analysis** — correlate structural entropy with experimental indel rates, regress out sequence-level confounders (SSC), and classify active vs inactive guides using logistic and linear models

## Repository Layout

```
CRISPR-Structure-development/
├── pdb_files/               # Core pipeline scripts (sequence → structure → analysis)
│   ├── 150_sequence/        # sgRNA sampling, FASTA parsing, Cas12f expansion
│   ├── rna_data/            # Latest RNAnneal delivery & interaction analysis
│   └── RNAnneal_LiLab_*/   # Archived RNAnneal delivery (structure reps)
├── SpCas9_2019/             # SpCas9 dataset utilities
│   └── pdb/                 # Cas-OFFinder input builder, merge, RNAfold analysis
├── RNAnneal_LiLab_2026-03-27/  # Packaged structure delivery (March 2026)
├── rna_data/                # Latest RNAnneal delivery (April 2026)
│   └── RNAnneal_LiLab_2026-04-24/
├── off-target-sequence-structure/  # Off-target mismatch alignment
└── pdb_copy.sh              # HPC → local PDB file transfer script
```

## Pipeline Stages at a Glance

| Stage | Folder | Key Scripts |
|-------|--------|-------------|
| Guide curation & sampling | `pdb_files/` | `generated_fasta_parser.py`, `PAM_finder.py` |
| Cas12f scaffold expansion | `pdb_files/150_sequence/` | `automation_Cas12f.py`, `CAs12f_trimming_proof_of_concept.py` |
| Sequence scoring | `pdb_files/` | `Azimuth_scoring.py`, `SSC_update.py` |
| Structure entropy extraction | `pdb_files/` | `complete_entropy_pdb.py`, `extract_entropy.py` |
| RNAfold scaffold interaction | `SpCas9_2019/pdb/` | `rnafold_analysis.py` |
| Off-target alignment | `off-target-sequence-structure/` | `align_sequences.py` |
| Statistical modelling | `pdb_files/` | `Integrated_sequence_structure_entropy_calculation.R`, `Classification_entropy_metric.R` |
| Visualisation | `pdb_files/`, `rna_data/` | `Visuliaze_SSC_regress_out.py`, `logistic_plot.R`, `quadarant_global.R` |

## Dependencies

**Python** (≥ 3.8)
```
pandas, numpy, biopython, statsmodels, scikit-learn, seaborn, matplotlib, azimuth, ViennaRNA (RNA module)
```

**R** (≥ 4.0)
```
dplyr, tidyr, ggplot2, ROCR, lmtest, RColorBrewer
```

**External tools**
- [Cas-OFFinder](http://www.rgenome.net/cas-offinder/) — genome-wide off-target search
- [RNAnneal](https://rnaanneal.org/) — sgRNA 3-D structure prediction
- [UCSF ChimeraX](https://www.cgl.ucsf.edu/chimerax/) — structure visualisation

## Quick Start

```bash
# 1. Clone and enter the repo
git clone https://github.com/vipinmenon1989/CRISPR-Sturcture-development.git
cd CRISPR-Sturcture-development

# 2. Install Python dependencies
pip install pandas numpy biopython statsmodels scikit-learn seaborn matplotlib openpyxl

# 3. Run guide curation (SpCas9 example)
cd pdb_files
python generated_fasta_parser.py        # Filter + sample 150 guides
python PAM_finder.py                    # Validate NGG PAM sites
python Azimuth_scoring.py              # Predict on-target efficiency

# 4. Extract structural entropy after RNAnneal modelling
python complete_entropy_pdb.py          # B-factor entropy from PDB files
python SSC_update.py                    # Append SSC sequence scores

# 5. Run integrated statistical analysis in R
Rscript Integrated_sequence_structure_entropy_calculation.R
Rscript Classification_entropy_metric.R
```

## Branch

This is the `integrated-pipeline` branch, which consolidates sequence analysis, structural modelling outputs, and statistical workflows into a single cohesive pipeline.

## Contact

Vipin Menon — Li Lab  
Repository: [CRISPR-Sturcture-development](https://github.com/vipinmenon1989/CRISPR-Sturcture-development)
