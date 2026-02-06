# HF ETIOLOGY Data Processing

## VCF Filtering and Annotation Pipeline

A hybrid Python and `bcftools` pipeline designed to filter raw VCF files for rare, high-impact SNVs. This tool automates the transition from raw genomic data to a clean, sample-encoded CSV for downstream statistical analysis.


## Workflow Summary

The pipeline processes genomic data through four distinct stages:

### 1. Hard Quality Filtering
Using `bcftools`, we filter for high-confidence calls:
* **QUAL >= 30**: Minimum Phred-scaled quality score.
* **FILTER = "PASS"**: Excludes variants that failed internal caller quality checks.

### 2. Normalization
* **Decomposition**: Splits multiallelic sites into individual biallelic records using `bcftools norm -m -any`. This ensures each row in the final output represents a single variant-allele pair.

### 3. Biological & Frequency Constraints
The script utilizes `cyvcf2` for fast iteration to apply the following logic:
* **SNV Only**: Filters out Indels and structural variants.
* **Rare Variants**: Filters for Allele Frequency (AF) $\le 0.01$.
* **Functional Impact**: Retains only `missense` or `stop_gained` consequences.

### 4. dbSNP & CADD Annotation
Leveraging the `MyVariant.info` API, the pipeline enriches the data with:
* **Refined Gene Symbols**: Verified via dbSNP.
* **Pathogenicity**: Putative impact and SnpEff effects.
* **Genotype Encoding**: Translates VCF genotypes into a numerical format:
    * `0`: Wild Type (0/0)
    * `1`: Heterozygous (0/1)
    * `2`: Homozygous Alternate (1/1)

---

## Prerequisites

### Bioinformatic Tools
* **bcftools**:```bash
  conda install -c bioconda -c conda-forge bcftools=1.20

### Python Environment
```bash
pip install cyvcf2 pandas tqdm myvariant numpy
