# AMLclassifier
A classifier for Single-cell RNA sequencing hematopoietic stem/progenitor cells.

Acute myeloid leukaemia (AML) is an aggressive cancer of hematopoietic stem/progenitor cells originating in the bone marrow. Single-cell RNA sequencing (scRNAseq) is increasingly used to unbiasedly characterize patient-derived AML cells to address a variety of biological questions. Annotations of these cells rely on expert-based curations which are time-consuming and not generalizable. There is thus an important need for an accurate and automated method to annotate the diverse cell types of normal hematopoietic cells and in AML samples. We have developed an AML classifier which successfully annotates these cells.
We used a healthy bone marrow scRNAseq reference dataset for training this classifier. To create this reference, we used FASTQ files of HCA[^1], ran them using Cell Ranger version 5 (10X Genomics) GRCh38-2020-A (Ensemble 98) standard reference. Gene expressions were normalized by sequencing depth, scaled to a constant depth 10,000, as it is the approximate median UMI in our AML cohort ran using our current chemistry, and log-transformed. After which, an extensive annotation effort was deployed using both previous annotations released for this dataset, Hay, et. al[^2] which has a good granularity for the progenitor population and DISCO[^3] which has better granularity in the differentiated cell types, as well as expert curation by our group. 
A subset (~64K cells) of the re-annotated cohort was used for training of Artificial neural network (ANN) algorithms. The classifier can assign each cell to one of 53 cell types. 
 
More details to be followed.
 
 
## Authors / Credits
 
- [Azer Farah] 
- [Banafsheh Khakipoor](https://github.com/BanafshehKhaki)
- [Olivier Gingras]( https://github.com/gingo00)
- [Veronique Lisi](https://github.com/veroniquelisichusj)
- Vincent-Philippe Lavallee



 
  
[^1]: https://explore.data.humancellatlas.org/projects/cc95ff89-2e68-4a08-a234-480eca21ce79
[^2]: Hay, Stuart B et al. “The Human Cell Atlas bone marrow single-cell interactive web portal.” Experimental hematology vol. 68 (2018): 51-61. doi:10.1016/j.exphem.2018.09.004
[^3]: Li, Mengwei et al. “DISCO: a database of Deeply Integrated human Single-Cell Omics data.” Nucleic acids research vol. 50,D1 (2022): D596-D602. doi:10.1093/nar/gkab1020
 
