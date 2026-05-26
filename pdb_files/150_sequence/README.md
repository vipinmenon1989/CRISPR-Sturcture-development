# 150_sequence — Guide Curation & Cas12f Scaffold Expansion

This subdirectory handles two closely related tasks: sampling a stratified 150-guide panel from the SpCas9 dataset, and expanding Cas12f scaffold truncation series into a structured input table for RNAnneal.

## Scripts

### SpCas9 Guide Sampling

| Script | Purpose |
|--------|---------|
| `generated_fasta_parser.py` | Filters the SpCas9 dataset by PAM (NGG at positions 25–26), bins guides by indel rate into 4 quartiles, and samples a stratified set of 150 guides |
| `generated_fasta_parser_2020.py` | 2020 dataset variant of the above |
| `test.py` | Lightweight integration test for the parser logic |
| `test_standard.py` | Test variant for the `_standard` column schema |

Scripts ending in `_standard` operate on datasets where column names follow a different convention (e.g. `TargetSequence(RNAversion)` instead of `sequence`).

### Cas12f Scaffold Expansion

| Script | Purpose |
|--------|---------|
| `automation_Cas12f.py` | Reads `Cas12f1_tracr_spacer_truncation.xlsx` + `truncation_series.csv` and produces an expanded `Cas12f1_expanded_final.csv` with one row per guide–scaffold combination |
| `automation_Cas12f_standard.py` | Variant for alternative column naming |
| `CAs12f_trimming_proof_of_concept.py` | Proof-of-concept script for incremental 3′ scaffold truncation logic |
| `CAs12f_trimming_proof_of_concept_standard.py` | Standard variant |

## Inputs

| File | Description |
|------|-------------|
| `SpCas9_Indel_2020.csv` | Full SpCas9 sequence + indel dataset |
| `Cas12f1_tracr_spacer_truncation.xlsx` | Cas12f guide + scaffold source table |
| `truncation_series.csv` | Scaffold truncation steps with sequences and labels |

## Outputs

| File | Description |
|------|-------------|
| `sampled_150_guides.csv` / FASTA | Stratified 150-guide panel ready for RNAnneal submission |
| `Cas12f1_expanded_final.csv` | All target × scaffold combinations for Cas12f modelling |

## Sampling Strategy

The SpCas9 parser bins experimental indel rates into 4 equal-width bins and samples from each bin according to these targets:

| Bin (Indel %) | Target count |
|---------------|-------------|
| Low (0–25%) | 45 |
| Medium-low (25–50%) | 22 |
| Medium-high (50–75%) | 38 |
| High (75–100%) | 45 |

If a bin has fewer guides than the target, sampling is done with replacement.
