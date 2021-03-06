This notebook contains high level information on the bioinformatics pipeline that was used for the analysis reported in **Claeys A, Luijts T, Marchal K, Van den Eynden J. Low immunogenicity of common cancer hot spot mutations resulting in false immunogenic selection signals, 2021**

# Environment 

Analysis was performed in a Conda environment. See **mhc_driver.yml** for details. **scripts/Rpacks** describes R packages that were installed independently.

# Data

## Downloaded data

The following data were downloaded from external sources:
  - TCGA mutation data: MC3 calls
  - TCGA cancer ids
  - Cancer Gene Census data from cosmic
  - 1000 genomes HLA alleles
  - Cancer Cell Line Encyclopedia (CCLE): mutation data and HLC calls

Download scripts are provided under *scripts/downloads/* in the corresponding folders.

## Processed data

Processed data (referred to in data/) are available at Zenodo 10.5281/zenodo.4419692

# Data processing

## Mutation matrix

A 10,295 (TCGA samples) x 688 (driver mutations) mutation matrix was created where each cell indicates whether the mutations is present for the specified sample or not.

Driver mutations were selected using the following criteria:
- Missense mutation
- Gene known as CGC gene (v91)
- Recurrency minimal 5 in complete dataset

### TCGA patients

```{r,eval=F}
  # Get the required variables from mc3 maf files
  source("scripts/get_mut_data.R")
  # Create mutation matrix
  source("scripts/create_mut_matrix.R")
```

## HLA matrix

A corresponding 10,295 (TCGA samples) x 688 (driver mutations) HLA matrix was created where each cell contains the PHBR score as an indicator of HLA affinity of the translated peptides.

```{r,eval=F}
  source("scripts/create_HLA_matrix.R")
```

## Mut & HLA matrix per cancer type

```{r,eval=F}
  source("scripts/create_data_per_cancer_type.R")
```

## PHBR for prototypical MHC-I and MHC-II genotypes

PHBR was calculated for each mutation for the the prototypical genotype, for mutated as well as wild type peptides.

```{r,eval=F}
  source("scripts/get_HLA_proto.R")
```

### Virtual patients

```{r,eval=F}
  source("scripts/create_vp_mut_matrix.R")
  source("scripts/create_vp_HLA_matrix.R")
```

## Random substitutions

15,000 random substitutions were generated: 150 different substitutions, 100 each

```{r,eval=F}
  source("scripts/get_HLA_subst.R")
```

## Other mutation datasets

Randomly select:
- 500 non-recurrent not in CGC
- 500 non-recurrent in CGC
- 500 recurrent not in CGC

```{r, eval=F}
  source("scripts/get_mutation_datasets.R")
```

Add 500 random unobserved mutations, merge and add evolutionary conservation scores

```{r, eval=F}
  source("scripts/merge_mutation_datasets.R")
```

## CCLE data

Merge CCLE mutation data and HLA cells. Create HLA and mutation matrices.

```{r, eval=F}
  source("scripts/create_CCLE_data.R")
```

# Analysis as reported in the manuscript

## 1) PHBR for observed/unobserved mutations in TCGA data

### Description of mutation data
```{r}
  source("scripts/manuscript_mut_analysis.R")
```

### Correlation mut - PHBR

#### Boxplots

```{r}
  source("scripts/manuscript_mut_HLA_analysis.R")
```

#### Logistic regression

Both pan cancer and per cancer analysis

Logistic regression analysis: requires some time

```{r, eval=F}
  # Within mutation model
  source("scripts/manuscript_logreg_wm.R")
  # Within patient model
  source("scripts/manuscript_logreg_wp.R")
```

Plot

```{r}
  source("scripts/manuscript_mut_HLA_perCancer_analysis.R")
```

## 2) Virtual patient baseline analysis

```{r}
  source("scripts/manuscript_vp_analysis.R")
```

## 3) Leave one out analysis: identification of 13 mutations

LOO logistic regression analysis: requires some time

```{r, eval=F}
  # Get baseline ORs
  source("scripts/manuscript_vp_logreg_wp.R")
  # LOO
  source("scripts/manuscript_LOO_analysis.R")
  # Select 13 mutations
  source("scripts/manuscript_LOO_select_mut13.R")
  # Get OR after exclusion of 13 mutations
  source("scripts/manuscript_LOO_select_mut13.R")
```

Plot
```{r}
  source("scripts/manuscript_LOO_plot.R")
```

## 4) dPHBR analysis

### Baseline dPHBR

```{r}
  source("scripts/manuscript_dPHBR.R")
```

### Random substitution analysis

```{r}
  source("scripts/manuscript_dPHBR_GPPM.R")
```

## 5) AA enrichment analysis

### WT PHBR analysis

```{r}
  source("scripts/manuscript_vp_analysis_OR_wt.R")
```

### Linear regression AA - PHBR

```{r}
  source("scripts/manuscript_lm_aa_PHBR.R")
```

### AA enrichment

```{r}
  source("scripts/manuscript_aa_enrichment.R")
```

## S1) Methodology

```{r}
  source("scripts/manuscript_methodology.R")
```

## S2) Suppl. analysis on TCGA data

```{r, eval=F}
  # WT affinities
  source("scripts/get_HLA_TCGA_wt.R")
  # Get baseline ORs
  source("scripts/manuscript_TCGA_logreg_wp.R")
  # LOO
  source("scripts/manuscript_LOO_analysis_TCGA.R")
```

Plot
```{r}
  source("scripts/manuscript_TCGA_analysis.R")
```

## S3) Suppl. analysis using CCLE data

```{r}
  source("scripts/manuscript_CCLE_analysis.R")
```

## S4) Suppl. analysis using 1kg population alleles

```{r, eval=F}
  # Get HLA alleles & perform logreg
  source("scripts/manuscript_1kg.R")
  # LOO
  source("scripts/manuscript_LOO_analysis_1kg.R")
```

Plot
```{r}
  source("scripts/1kg_analysis.R")
```

## S5) Suppl. analysis on individual alleles

Calculate PBR
```{r, eval=F}
  source("scripts/manuscript_alleles_OR.R")
```

Plot
```{r}
  source("scripts/manuscript_alleles_OR_analysis.R")
```

## S6) Suppl. analysis conservation scores

Plot
```{r}
  source("scripts/manuscript_cons_analysis.R")
```
