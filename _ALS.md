# ALS Transcriptomic Biomarker Discovery

Blood-based biomarker discovery for amyotrophic lateral sclerosis (ALS) using RNA-seq data, built as an independent project during my M.Tech in Bioinformatics.

## Why this project

ALS is usually diagnosed late, after a meaningful chunk of motor neuron damage has already happened, partly because there's no reliable, non-invasive early marker in routine use. Most published biomarker work leans on CSF or tissue samples, which aren't practical for regular screening. I wanted to see how far a blood-based approach could go, using public RNA-seq data and a pipeline modeled loosely on biomarker discovery work done for Alzheimer's disease.

## Data

RNA-seq datasets pulled from NCBI GEO, blood-derived samples from ALS patients and matched controls. (Add the specific GEO accession numbers here once you finalize which datasets you used.)

## Pipeline

1. **Preprocessing and quality control** on raw count data
2. **Differential expression analysis** to identify genes distinguishing ALS samples from controls
3. **Feature selection** using machine learning methods to narrow the differentially expressed genes down to a smaller, more useful candidate set
4. **Functional enrichment analysis** to check whether the candidate genes cluster around biologically relevant pathways rather than showing up by chance
5. **Validation**, cross-checking the resulting gene signature against patterns reported in comparable AD biomarker studies

## What I found

(Fill in with your actual results, number of candidate genes, key pathways, validation outcome, once you have final numbers to report.)

## Tools

Python, GEOquery/GEO datasets, DESeq2-style differential expression methods, scikit-learn for feature selection, standard enrichment tools (adjust this list to match what you actually used).

## Notes

This was a self-directed project, not part of an assigned coursework brief. The biggest challenge was working with messy, inconsistent metadata across GEO submissions, which ended up teaching me more about real-world data cleaning than the modeling itself did.
