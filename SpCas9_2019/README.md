# SpCas9_2019 — SpCas9 Dataset Utilities

This directory contains utilities for processing the 2019 SpCas9 structural dataset — specifically building Cas-OFFinder input files, extracting and merging sequence/indel data, running RNAfold-based scaffold interaction analysis, and visualising results.

## Directory Structure

```
SpCas9_2019/
└── pdb/
    ├── casmaker.py          # Build Cas-OFFinder 23-nt input from CSV
    ├── extract_indel.py     # Extract sgRNA ID + indel rate from TSV
    ├── extract_sgRNA.py     # Extract sequence column; convert U → T
    ├── merge_file.py        # Inner join two sequence files on normalised sequence key
    ├── merge_scc.py         # Outer join sequence + SSC data files
    ├── plot_interaction.py  # Heatmap of TL/SL1/SL2/SL3 interaction profiles
    ├── rnafold_analysis.py  # ViennaRNA suboptimal structure → stem-loop interaction matrix
    ├── sgrna_extractor.py   # Extract specific sgRNA IDs from the master CSV
    └── ultra_merge.py       # Final ID-based merge of SSC scores + structural data
```

## Script Details

### casmaker.py
Reads a CSV with `ID` and `guide` columns, converts RNA spacers to DNA, appends `NGG` template, and writes a Cas-OFFinder-compatible input file including the genome directory path.

```bash
# Edit the file paths inside the script, then:
python casmaker.py
```

### extract_indel.py
Extracts `ID` and `indel` columns from a TSV/CSV with auto-detected separators and renames them to `sgRNA` / `Indel_Rate` for downstream compatibility.

```bash
python extract_indel.py input.tsv
```

### rnafold_analysis.py
Uses the ViennaRNA Python bindings to fold each sgRNA sequence and compute whether the spacer region (positions 1–20) interacts with four scaffold stem-loops:

| Region | Residues |
|--------|---------|
| TL (tetraloop) | 32–37 |
| SL1 (stem-loop 1) | 54–60 |
| SL2 (stem-loop 2) | 71–76 |
| SL3 (stem-loop 3) | 88–90 |

Outputs a binary interaction matrix across the top 5 suboptimal structures per guide.

```bash
python rnafold_analysis.py SpCas9_2019_150_sequence.csv
```

### plot_interaction.py
Generates a seaborn heatmap from the interaction CSV produced by `rnafold_analysis.py`.

```bash
python plot_interaction.py sgrna_interactions.csv
```

### merge_file.py / merge_scc.py / ultra_merge.py
Three-stage merging pipeline:
1. `merge_file.py` — inner join on normalised sequence (U→T)
2. `merge_scc.py` — outer join sequence data with SSC scores
3. `ultra_merge.py` — final strict inner join on `ID` to produce `Final_Merged_CRISPR_Data.txt`

## Dependencies

```
pandas, ViennaRNA (RNA python module), seaborn, matplotlib
```

Install ViennaRNA:
```bash
conda install -c bioconda viennarna
```

## Input Files

| File | Description |
|------|-------------|
| `SpCas9_2019_150_sequence.csv` | Master guide list with `ID`, `sgRNA`, `sequence`, `indel` columns |
| `SSC_sequence.out` | SSC scores (whitespace-separated) |
| Cas-OFFinder output `.txt` | Off-target hits from Cas-OFFinder |
