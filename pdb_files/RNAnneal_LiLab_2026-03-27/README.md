# RNAnneal_LiLab_2026-03-27 — March 2026 Structure Delivery

This directory contains packaged RNAnneal representative structures and analysis scripts for the Li Lab (March 2026 delivery). It is the predecessor to the April 2026 delivery in `rna_data/RNAnneal_LiLab_2026-04-24/`.

## Directory Structure

```
RNAnneal_LiLab_2026-03-27/
├── manifest.csv          # Per-sequence packaging summary
├── chimerax/
│   ├── color_ie_bins.cxc           # ChimeraX coloring script (IE-bin based)
│   └── open_and_color_all.cxc     # Open all representative PDBs + apply coloring
└── sequence/
    ├── input.fa                    # Input RNA sequences submitted to RNAnneal
    ├── 150_cas_off_finder_input.txt  # Cas-OFFinder input for this guide panel
    ├── cas-off-fincer-builder.py   # Script to build Cas-OFFinder input from FASTA
    └── reps/                       # Representative PDB models + analysis scripts
        ├── pdb_files/              # Full 150-guide PDB structure set (rank01 per guide)
        ├── chimera_SpCas9_2019/    # SpCas9 2019 subset PDBs + interaction analysis
        │   └── 2020_interaction/   # Extended distance and stacking interaction data
        ├── CFD.py                  # Cut Frequency Determination scoring
        ├── Cumulative_CFD.py       # Cumulative CFD score across off-target panel
        ├── SpCas9_2019_analysis_off_target_vs_structure.py   # Main off-target vs structure analysis
        ├── SpCas9_2019_high_low.py  # Stratify guides into high/low structural groups
        ├── Structural_enrrichment.py  # Fisher's exact test for structural feature enrichment
        ├── RMSD_pdb_global.py      # Global RMSD computation across ranked PDB models
        ├── pdb_entropy.py          # Per-residue B-factor entropy extraction
        ├── 150_heatmap_probability.py  # Off-target probability heatmap for 150 guides
        ├── analysis_plot.py        # General correlation and visualisation
        ├── all_scaffold_interaction.py  # Scaffold-wide interaction summary
        ├── locing.py               # Structural locking metric computation
        └── extrac_extreme.py       # Extract highest/lowest structural entropy guides
```

## Key Data Files

| File | Description |
|------|-------------|
| `manifest.csv` | Sequence ID, length, rep count, packaging status, source paths |
| `sequence/input.fa` | FASTA of 150 sgRNA sequences submitted to RNAnneal |
| `reps/Master_CFD_Scored_OffTargets.csv` | CFD-scored off-target table for all 150 guides |
| `reps/Master_150_Guide_Correlation_Matrix.csv` | Pairwise correlation matrix across guides |
| `reps/Scaffold_Ensemble_RMSD_Profiles.csv` | RMSD across the structural ensemble per guide |
| `reps/Structural_Stats_Comparison.csv` | Structural feature statistics for high vs low activity groups |
| `reps/Structural_Enrichment_Results.csv` | Fisher's exact enrichment results |
| `reps/merged_structural_offtarget_analysis.csv` | Combined structural + off-target risk table |
| `reps/pdb_entropy_extracted.csv` | Extracted per-residue entropy values |

## ChimeraX Scripts

Open all representative structures and apply interaction-entropy (IE) bin-based colouring:

```bash
# In ChimeraX command line:
open chimerax/open_and_color_all.cxc
```

The `color_ie_bins.cxc` script applies a colour gradient from low (blue) to high (red) structural entropy.

## Analysis Highlights

- **CFD scoring** (`CFD.py`, `Cumulative_CFD.py`): quantifies off-target cleavage risk using the Cutting Frequency Determination model
- **Structural enrichment** (`Structural_enrrichment.py`): tests whether high-activity guides are enriched for specific scaffold interaction patterns using Fisher's exact test
- **RMSD profiles** (`RMSD_pdb_global.py`): measures structural flexibility across RNAnneal-ranked models to identify conformationally locked guides
- **Off-target vs structure** (`SpCas9_2019_analysis_off_target_vs_structure.py`): correlates structural entropy with off-target risk scores

## Notes

- PDB files follow the naming convention `sgRNA_{ID}_rank{N:02d}.pdb`
- Only the top-ranked representative (`rank01`) is included in the root `pdb_files/` folder; additional ranks are in the subdirectories
- This delivery has been superseded by the April 2026 delivery but is retained for reproducibility
