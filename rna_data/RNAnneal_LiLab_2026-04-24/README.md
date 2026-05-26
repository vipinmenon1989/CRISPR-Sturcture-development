# RNAnneal_LiLab_2026-04-24 — April 2026 Structure Delivery

This directory contains the latest RNAnneal representative structures and accompanying analysis scripts for the Li Lab CRISPR sgRNA modelling project (April 2026 delivery).

## Directory Structure

```
RNAnneal_LiLab_2026-04-24/
└── sequence/
    ├── fasta_csv.py              # Convert FASTA input to CSV for downstream processing
    ├── plot_interaction_map.py   # Plot spacer–scaffold interaction map
    ├── rnafold_analysis.py       # ViennaRNA suboptimal folding → stem-loop interaction matrix
    └── reps/
        ├── spacer_scaffold_interaction.py    # Compute spacer vs scaffold contact profiles from PDB
        └── chimera/
            ├── barplot_interactions.py       # Bar chart of interaction frequencies per scaffold region
            ├── plot_distance_heatmap.py      # Atom-distance heatmap across sgRNA residues
            └── plot_interaction_map.py       # ChimeraX-compatible interaction map plotting
```

## Scripts

### sequence/fasta_csv.py
Converts a multi-sequence FASTA file to a two-column CSV (`sgRNA`, `Sequence`) suitable for pipeline input.

```bash
python fasta_csv.py input.fasta output.csv
```

### sequence/rnafold_analysis.py
Uses ViennaRNA to compute suboptimal secondary structures and extract spacer–scaffold stem-loop interactions (TL, SL1, SL2, SL3). Outputs a binary interaction matrix per guide across the top 5 ranked structures.

```bash
python rnafold_analysis.py guides.csv
```

### sequence/plot_interaction_map.py
Visualises the overall pattern of spacer–scaffold contacts across the guide library.

### reps/spacer_scaffold_interaction.py
Computes direct atom-level contacts between the spacer and scaffold regions from PDB structure files. Feeds data into the ChimeraX visualisation scripts.

### reps/chimera/ — ChimeraX Visualisation Scripts

| Script | Output |
|--------|--------|
| `barplot_interactions.py` | Bar chart of interaction frequency per stem-loop region |
| `plot_distance_heatmap.py` | Heatmap of inter-residue distances across sgRNA |
| `plot_interaction_map.py` | Interaction contact map for ChimeraX-parsed PDB data |

## Relationship to Previous Delivery

This is the successor to `RNAnneal_LiLab_2026-03-27`. The April delivery adds:
- Updated structural representatives after re-modelling
- Extended ChimeraX interaction visualisation scripts
- Integrated RNAfold analysis directly within the delivery directory

## Dependencies

```
python >=3.8, pandas, ViennaRNA (RNA module), matplotlib, seaborn
```
