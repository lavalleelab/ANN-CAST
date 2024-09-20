[![DOI](https://zenodo.org/badge/726190560.svg)](https://zenodo.org/doi/10.5281/zenodo.10476103)

#  ANN-CAST : Automated Neural Network Classification for AML Single-cell Transcriptomes 

Acute myeloid leukaemia (AML) is an aggressive cancer of hematopoietic stem/progenitor cells originating in the bone marrow. Single-cell RNA sequencing (scRNAseq) is increasingly used to unbiasedly characterize patient-derived AML cells to address a variety of biological questions. Annotation of these cells relies on expert-based curation which is time-consuming and not generalizable. There is thus an important need for an accurate and automated classification of cells derived from normal bone marrow or AML samples. To this end, we developed a neural network based classifier that successfully annotates these cells.
We trained this classifier using  single cell RNA sequencing data generated from 8  healthy donors and obtained from the Human Cell Atlas (HCA) consortium[^1].  We reprocessed the original FASTQ files using Cell Ranger version 5 (10X Genomics) together with the GRCh38-2020-A (Ensemble 98) standard reference. Gene expression matrices were normalized by sequencing depth, scaled to a constant depth of 10 000, and log-transformed. Low quality cells and doublets were filtered out. An extensive annotation effort was deployed by combining  two previously published annotations released for this dataset[^2] [^3]. The resulting dataset was subsetted to a maximum of 3000 cells per cell type, resulting in a final dataset of 64 000 cells. This dataset was used for training an artificial neural network (The resulting classifier was validated on an independent dataset.  
 
### Tutorials:

- [Python tutorial](https://github.com/lavalleelab/AMLclassifier/blob/main/Classifier_python.ipynb)
- [R tutorial](https://github.com/lavalleelab/AMLclassifier/blob/main/Classifier_R.ipynb)

 
More details to be followed.

# How to cite 
Please cite our preprint that will be available shortly.
 
## Authors / Credits


- [Banafsheh Khakipoor](https://github.com/BanafshehKhaki)
- [Veronique Lisi](https://github.com/veroniquelisichusj)
- [Azer Farah](https://github.com/azer-farah)
- [Olivier Gingras](https://github.com/gingo00)
- Vincent-Philippe Lavallee


 
  
[^1]: https://explore.data.humancellatlas.org/projects/cc95ff89-2e68-4a08-a234-480eca21ce79
[^2]: Hay, Stuart B et al. “The Human Cell Atlas bone marrow single-cell interactive web portal.” Experimental hematology vol. 68 (2018): 51-61. doi:10.1016/j.exphem.2018.09.004
[^3]: Li, Mengwei et al. “DISCO: a database of Deeply Integrated human Single-Cell Omics data.” Nucleic acids research vol. 50,D1 (2022): D596-D602. doi:10.1093/nar/gkab1020
 
