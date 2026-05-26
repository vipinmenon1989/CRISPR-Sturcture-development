# pdb_files — Core Pipeline Scripts

This directory contains the primary analysis scripts that drive the integrated sequence–structure pipeline. Scripts come in two variants: the standard SpCas9 workflow and an extended `_standard` variant for cross-dataset validation.

## Directory Structure

```
pdb_files/
├── 150_sequence/                    # Guide curation & Cas12f scaffold expansion
├── rna_data/                        # RNAnneal April 2026 delivery (latest)
├── RNAnneal_LiLab_2026-03-27/      # RNAnneal March 2026 delivery (archived)
│
│── Sequence preparation
├── generated_fasta_parser.py        # Filter SpCas9 guides by PAM; stratified 150-guide sampling
├── PAM_finder.py                    # Validate NGG PAM (positions 25–26) in 30-nt sequences
├── sequence_seprator.py             # Separate spacer and scaffold sequence fields
├── merge_files_spacer.py            # Merge spacer-level data files
├── sequence_tester.py               # Sanity-check sequence formatting
│
│── Sequence scoring
├── Azimuth_scoring.py               # Run Azimuth on-target efficiency predictions
├── SSC_update.py                    # Append SSC (Sequence Scan for CRISPR) scores
├── extract_sgRNA.py                 # Extract sgRNA sequences from PDB-derived tables
├── pdb_nucleotide_extractor.py      # Pull nucleotide sequences directly from PDB ATOM records
│
│── Structural entropy extraction
├── complete_entropy_pdb.py          # Full B-factor entropy: sum, median, max, min, StDev, IQR, range + DG_UNFOLD
├── extract_entropy.py               # Region-level entropy: TL, SL1, SL2, SL3 (absolute, relative, super-relative)
│
│── Regression & modelling
├── regress_out_SSC_linear.py        # Linear regression: structural entropy controlling for SSC
├── regress_out_SSC_logistic.py      # Logistic regression: activity classification controlling for SSC
├── Visuliaze_SSC_regress_out.py     # Scatter/residual plots after SSC regression
├── advanced_strict_data.py          # Strict per-region entropy metrics for advanced modelling
├── advanced_strict_region.py        # Region-specific strict analysis variant
│
│── R statistical analysis
├── Integrated_sequence_structure_entropy_calculation.R   # Full logistic model + ROC + LRT
├── Classification_entropy_metric.R                       # Entropy feature classification with Youden's J threshold
├── advanced_corraltion_strict.R                          # Per-region correlation scorecard (28 features)
├── correaltion_entropy_indel.R                           # Pearson/Spearman entropy vs indel correlations
├── cumulative_entropy_correlation.R                      # Cumulative entropy trajectory vs activity
├── log_likelihood_test.R                                 # LRT comparing nested models
├── logistic_plot.R                                       # Logistic regression visualisation
├── regress_linear.R                                      # Linear regression plots
├── quadarant_global.R                                    # Quadrant scatter (structure vs sequence score)
├── export_domain.R                                       # Export domain-level feature tables
├── Metric_sample_size.R                                  # Power analysis / sample size estimation
│
│── Data files (inputs/outputs)
├── entropy_cumulative_stats.tsv            # Raw cumulative entropy output
├── entropy_cumulative_stats_update_final.tsv  # Final merged entropy + SSC + indel table
└── update_indel_to_entropy.py             # Utility to append indel rates to entropy tables
```

## Key Workflow

### 1. Guide curation
```bash
python generated_fasta_parser.py   # Reads SpCas9_Indel_2020.csv → filters PAM → samples 150 guides
python PAM_finder.py               # Produces PAM_Validation_Results.tsv
python sequence_seprator.py        # Separates spacer / scaffold fields
```

### 2. Sequence scoring
```bash
python Azimuth_scoring.py          # Requires azimuth package; outputs Azimuth.tsv
python SSC_update.py               # Merges SSC_sequence.out → entropy_cumulative_stats_update_final.tsv
```

### 3. Structural entropy (after RNAnneal modelling)
```bash
python complete_entropy_pdb.py     # Reads PDB directory → entropy_cumulative_stats.tsv
python extract_entropy.py          # Extracts region-level (TL/SL1/SL2/SL3) entropy tables
```

### 4. Statistical analysis
```r
Rscript Integrated_sequence_structure_entropy_calculation.R
Rscript Classification_entropy_metric.R
Rscript advanced_corraltion_strict.R
```

## Input Files Expected

| File | Description |
|------|-------------|
| `SpCas9_Indel_2020.csv` | SpCas9 dataset with `sequence` and `indel` columns |
| `SpCas9_2020_sequence.csv` | 30-nt sequences with `TargetSequence(RNAversion)` |
| `SSC_sequence.out` | SSC scores (tab/space-separated) |
| PDB directory | Folder of ranked `.pdb` files from RNAnneal |

## Output Files

| File | Description |
|------|-------------|
| `Azimuth.tsv` | On-target efficiency scores |
| `entropy_cumulative_stats.tsv` | Per-guide entropy statistics + DG_UNFOLD |
| `entropy_cumulative_stats_update_final.tsv` | Final merged table (entropy + SSC + indel) |
| `entropy_advanced_strict.tsv` | Per-region (TL/SL1/SL2/SL3) entropy scorecard |
| `PAM_Validation_Results.tsv` | PAM site validation summary |

## Notes

- Scripts ending in `_standard` are identical in logic but expect different input column names or file paths for cross-dataset validation runs.
- The `_standard_standard` and `_standard_standard_standard` variants are legacy iterative copies and can be consolidated — the most recent `_standard` version is the canonical one.
- `Azimuth_scoring.py` requires the `azimuth` package installed in the active Python environment.
