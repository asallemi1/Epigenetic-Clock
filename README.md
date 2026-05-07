# Epigenetic-Clock
Academic project developed for the study of DNA methylation-based biological age prediction using machine learning and epigenetic profiling technologies.  

The project focuses on building an **epigenetic clock** capable of estimating biological age from DNA methylation patterns measured at CpG sites. The workflow combines bioinformatics, statistical modeling, and biological interpretation to investigate the molecular mechanisms underlying aging.

The study includes:

* DNA methylation data retrieval and preprocessing
* Epigenetic clock construction using Elastic Net regression
* Model calibration and validation
* Comparison with Horvath's epigenetic clocks
* Biological and pathway enrichment analysis
* Investigation of age-associated methylation changes

---

# Project Overview

Epigenetics studies the mechanisms regulating gene expression without altering the DNA sequence itself. Among epigenetic mechanisms, **DNA methylation** plays a crucial role in controlling transcriptional activity and cellular function.

Epigenetic clocks are computational models capable of estimating an individual's **biological age** based on methylation patterns across specific CpG sites. Biological age may differ from chronological age and can provide insights into accelerated or decelerated aging processes.

The aim of this project is to:

* Leverage DNA methylation changes to identify molecular drivers of aging
* Build a robust and generalizable epigenetic clock
* Compare the proposed model with established Horvath's clocks
* Analyze biological pathways and genes associated with aging

---

# Workflow

| Phase                | Description                                     |
| -------------------- | ----------------------------------------------- |
| Data Retrieval       | Download and management of methylation datasets |
| Data Processing      | Cleaning, normalization, and imputation         |
| Model Development    | Elastic Net epigenetic clock training           |
| Calibration          | Bias correction through polynomial regression   |
| Testing & Comparison | Evaluation against Horvath's models             |
| Biological Analysis  | Functional enrichment and pathway analysis      |

---

# Datasets

Two public datasets were retrieved from the National Center for Biotechnology Information GEO repository using the `GEOquery` R package.

## Training and Validation Dataset — GSE246337

| Feature   | Value                     |
| --------- | ------------------------- |
| Samples   | 500                       |
| Tissue    | Blood                     |
| Age Range | 18–89 years               |
| Metadata  | Age, sex, race, ethnicity |

This dataset was used for:

* Training set creation
* Validation set creation
* Model calibration

---

## Test Dataset — GSE280465

| Feature         | Value                                                     |
| --------------- | --------------------------------------------------------- |
| Samples         | 163                                                       |
| Individuals     | 47                                                        |
| Tissues         | Saliva, blood, fingerstick blood, buccal epithelial cells |
| Selected Tissue | Blood only                                                |
| Age Range       | 19–70 years                                               |

This dataset was exclusively used as an external test set.

---

# DNA Methylation Data

The project uses data generated through the **Infinium MethylationEPIC v2.0 array platform**, which quantifies methylation levels for over 935,000 CpG sites across the human genome.

## Methylation Measurement Workflow

1. Genomic DNA undergoes bisulfite conversion
2. Unmethylated cytosines are converted to uracils
3. Methylated cytosines remain unchanged
4. DNA hybridizes on methylation-specific probes
5. Methylation levels are computed from fluorescence intensities and subsequently normalized into β-values ranging from:

| β-value | Interpretation     |
| ------- | ------------------ |
| 0       | Fully unmethylated |
| 1       | Fully methylated   |

---

# Data Processing

## Metadata Cleaning

Only relevant metadata fields were retained:

* GEO accession
* Subject ID
* Tissue
* Sex
* Race
* Ethnicity
* Sample ID
* Chronological age

---

## Data Normalization

β-values were obtained through a sample-wise normalization using the `sesame` package.

---

## Dataset Splitting

The dataset was divided into:

| Split          | Percentage |
| -------------- | ---------- |
| Training Set   | 80%        |
| Validation Set | 20%        |

The split preserved:

* Comparable age distributions
* Similar age standard deviations
* Balanced sex representation

---

## Missing Values Imputation

Missing values caused by low signal precision were imputed using:

| Method              | Configuration |
| ------------------- | ------------- |
| K-Nearest Neighbors | k = 5         |

---

# Epigenetic Clock Model

## Model Architecture

The epigenetic clock was developed using:

| Component        | Method                   |
| ---------------- | ------------------------ |
| Regression Model | Elastic Net              |
| Alpha            | 0.5                      |
| Validation       | 20-fold cross-validation |

Elastic Net regression was selected to handle the high-dimensional nature of DNA methylation data while balancing feature selection and regularization.

---

# Model Calibration

The initial model exhibited systematic prediction bias, particularly compression at extreme age values.

To address this issue:

* A post-hoc polynomial regression calibration was applied
* The same calibration strategy was used on the external test set

---

# Validation Results

## Overall Performance

| Metric      | Base Model | Calibrated Model |
| ----------- | ---------- | ---------------- |
| RMSE        | 3.98       | 3.49             |
| MAE         | 3.11       | 2.71             |
| Correlation | 0.98       | 0.99             |

---

## Results by Age Group

| Age Group | RMSE | MAE  |
| --------- | ---- | ---- |
| 18–30     | 2.79 | 2.23 |
| 31–50     | 2.97 | 2.25 |
| 51–70     | 3.33 | 2.41 |
| 71–89     | 4.40 | 3.69 |

Performance decreases slightly for older age groups, likely because the oldest age range predominantly includes individuals with a biological age lower than their chronological age, while subjects with accelerated biological aging may be underrepresented due to survival bias.

---

# Model Comparison

The calibrated model was compared against two established epigenetic clocks developed by Steve Horvath:

* Horvath Pan-Tissue Clock
* Horvath Skin & Blood Clock

## External Test Set Results

| Metric      | Calibrated Model | Horvath Skin & Blood |
| ----------- | ---------------- | -------------------- |
| RMSE        | 4.42             | 11.45                |
| MAE         | 3.35             | 10.87                |
| Correlation | 0.97             | 0.98                 |

---

# Delta Age Analysis

Δ-age was computed as:

```text
Predicted Age − Chronological Age
```

This metric was used to investigate biological aging acceleration and deceleration.

## Main Findings

* Distribution centered around zero indicates good calibration
* Slight positive skew suggests accelerated aging in some individuals
* Presence of outliers highlights strong biological variability

---

## Delta Age by Sex

The analysis revealed sex-related differences in biological aging:

* Females showed wider Δ-age variability
* Males exhibited more stable distributions

---

## Delta Age by Ethnicity

Ethnicity-based analysis highlighted differences in predicted biological age.

Example:

* Hispanic individuals tended to show lower predicted biological age values

---

# Biological Interpretation

## Probe Annotation and Enrichment

Selected probes were annotated using:

* `IlluminaHumanMethylationEPICv2`
* Ensembl database

Retrieved information included:

| Annotation Type      | Description               |
| -------------------- | ------------------------- |
| Chromosomal position | Genomic localization      |
| HGNC symbols         | Official gene names       |
| Gene biotype         | Functional classification |
| InterPro domains     | Protein domains           |
| Reactome pathways    | Biological pathways       |
| GO terms             | Functional ontology       |

---

# Age Predictive Genes

The model selected probes targeting **61 genes**, mainly located on chromosomes:

* 1
* 2
* 5
* 6
* 17

These chromosomes are enriched in regulatory and immune-related genes.

---

## Key Genes Identified

| Gene  | Biological Role                  |
| ----- | -------------------------------- |
| SOD2  | Oxidative stress protection      |
| SCN5A | Cardiac electrical signaling     |
| RPA2  | DNA replication and repair       |
| KLF14 | Lipid metabolism regulation      |
| SCGN  | Calcium-binding neuronal protein |

---

# Gene Biotypes

## Distribution

| Biotype        | Percentage |
| -------------- | ---------- |
| lncRNA         | 84.6%      |
| miRNA          | 7.7%       |
| Pseudogene     | 7.7%       |

The predominance of protein-coding genes suggests strong involvement of active cellular regulation mechanisms in aging.

---

# Functional Enrichment

## Main Biological Functions

The enriched genes were mainly associated with:

* Oxidative stress response
* Metal ion signaling
* Calcium binding
* Transcriptional regulation
* DNA repair
* Lipid metabolism

---

# Pathway Analysis

Reactome pathway enrichment identified several relevant pathways:

| Pathway               | Biological Relevance                     |
| --------------------- | ---------------------------------------- |
| Developmental Biology | Tissue development and differentiation   |
| Metabolism            | Cellular metabolic regulation            |
| Disease Pathways      | Neurodegenerative and metabolic diseases |
| Lipid Metabolism      | Energy storage and lipid regulation      |
| Immune System         | Inflammatory response                    |
| Signal Transduction   | Cell communication mechanisms            |

---

# Overlap with Horvath Models

Three genes were shared between:

* The calibrated model
* Horvath Pan-Tissue Clock
* Horvath Skin & Blood Clock

## Shared Genes

| Gene      | Role                                          |
| --------- | --------------------------------------------- |
| KLF14     | Lipid metabolism and transcription regulation |
| PRR34-AS1 | Long non-coding RNA                           |
| SCGN      | Calcium signaling and synaptic regulation     |

---

# Age-Associated Methylation Changes

Differential methylation analysis was performed using the `DMRcate` package.

## Main Findings

### Hypomethylated with Age

* PRR34-AS1
* SOD2
* SCN5A
* RPA2

These genes may represent potential aging biomarkers.

---

### Hypermethylated with Age

* KLF14
* SCGN
* RPA2

These changes may reflect reduced gene expression or broader regional methylation dynamics.

---

# Technologies Used

| Category                 | Technologies                            |
| ------------------------ | --------------------------------------- |
| Programming Languages    | R, Python                               |
| Data Retrieval           | GEOquery                                |
| Normalization            | sesame                                  |
| Machine Learning         | glmnet                                  |
| Statistical Analysis     | caret, base R                           |
| Imputation               | KNN                                     |
| Biological Annotation    | Ensembl, IlluminaHumanMethylationEPICv2 |
| Differential Methylation | DMRcate                                 |
| Visualization            | ggplot2                                 |

---

# Academic Context

This project was developed in an academic context focusing on computational biology, epigenetics, and machine learning applications for aging research.

---

# Project Conclusions

## Key Findings

* Age predictive genes exhibit predominant demethylation with aging
* Biological pathways associated with aging were successfully identified
* The calibrated model outperformed established Horvath clocks on the selected test dataset
* Key impacted biological functions include:

| Function                    | Associated Gene |
| --------------------------- | --------------- |
| Oxidative stress protection | SOD2            |
| Cardiac activity            | SCN5A           |
| DNA repair                  | RPA2            |
| Calcium binding             | SCGN            |
| Lipid metabolism            | KLF14           |

These findings are strongly connected with age-related diseases, including cardiovascular disorders, neurodegeneration, and cancer.

---

# License

This repository is intended for academic and educational purposes only.
