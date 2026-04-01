# Early In-Hospital Mortality Prediction from Clinical Notes Using Small Language Models

This repository contains the code for a Final Year Project on early patient risk prediction from **MIMIC-III** clinical notes using biomedical text embeddings, temporal trajectory features, and retrieval-based signals.

The main workflow focuses on **early in-hospital mortality prediction** from note text. An additional extension in `heart-failure-extension/` shows how the same general pipeline can be adapted to a second binary classification task: **admission-level heart failure detection**.

## Important note on data access

This repository **does not include MIMIC-III data**.

To run these notebooks, you must have **authorised access to MIMIC-III via PhysioNet** and local copies of the required CSV tables. You must point the notebooks to your own authorised copies of those files.

This repository also **does not include the fine-tuned model weights**, since they were derived from credentialed MIMIC data. The repository provides the code needed to recreate the model in a compliant environment.

## Repository contents

### Main mortality workflow
- `finetune_biomedbert_mimic_contrastive.ipynb`  
  Fine-tunes BiomedBERT on early MIMIC-III notes using leakage-controlled contrastive learning to produce **BiomedBERT-MIMIC-Contrastive**.

- `01_build_artifacts.ipynb`  
  Builds the reusable mortality-prediction artifacts:
  - cohort construction
  - note preprocessing
  - static embeddings
  - day-level embeddings
  - risk-trajectory features
  - retrieval features

- `02_train_evaluate_models.ipynb`  
  Loads the saved artifacts and runs downstream model training and evaluation, including ablations, calibration, and final test metrics.

### Heart failure extension
Located in `heart-failure-extension/`:

- `01_build_heart_failure_artifacts.ipynb`  
  Builds day-0 static text artifacts for admission-level heart failure classification.

- `02_train_evaluate_heart_failure.ipynb`  
  Loads the saved heart-failure artifacts and trains/evaluates downstream classifiers.

## Required MIMIC-III tables

### For fine-tuning and the main mortality workflow
- `ADMISSIONS.csv`
- `NOTEEVENTS.csv`

### For the heart failure extension
- `ADMISSIONS.csv`
- `NOTEEVENTS.csv`
- `DIAGNOSES_ICD.csv`
- `D_ICD_DIAGNOSES.csv`

## Recommended usage order

### 1. Fine-tune the encoder
Run:

`finetune_biomedbert_mimic_contrastive.ipynb`

This notebook fine-tunes BiomedBERT on early MIMIC-III notes and saves the resulting model folder for **BiomedBERT-MIMIC-Contrastive**.

### 2. Build mortality artifacts
Run:

`01_build_artifacts.ipynb`

This notebook uses the fine-tuned encoder and the authorised MIMIC-III tables to generate all reusable artifacts needed for downstream modelling.

### 3. Train and evaluate mortality models
Run:

`02_train_evaluate_models.ipynb`

This notebook loads the saved artifacts and performs downstream classification, ablation testing, calibration, and final evaluation.

### 4. Optional: run the heart failure extension
To test transferability of the pipeline to a second task, run:

- `heart-failure-extension/01_build_heart_failure_artifacts.ipynb`
- `heart-failure-extension/02_train_evaluate_heart_failure.ipynb`

This extension uses day-0 note text to detect whether heart failure is present in the admission coding record.

## Setup notes

Before running any notebook:

- obtain authorised MIMIC-III access through PhysioNet
- edit the path/configuration cells so they point to your local copies of the required MIMIC tables
- choose where you want artifacts and models to be saved
- ensure the downstream notebooks point to the artifacts produced by the build notebooks

## Reproducibility

The notebooks are split so that expensive preprocessing and embedding steps are performed once, saved to disk, and reused during downstream model development and evaluation.

## What is not included

The public repository does **not** include:
- MIMIC-III data
- derived artifact files
- saved checkpoints
- fine-tuned model weights

Only the code needed to recreate the workflow is provided.

## Project summary

The main project investigates whether early clinical notes can support meaningful patient risk prediction using:
- biomedical text embeddings
- temporal trajectory summaries
- retrieval-derived case-based signals

The heart failure extension shows that the same general framework can be adapted to another clinically meaningful binary classification task with minimal architectural change.
