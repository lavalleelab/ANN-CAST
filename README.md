# AMLclassifier
A classifier for Single-cell RNA sequencing hematopoietic stem/progenitor cells.

## Background
Acute myeloid leukemia (AML) is an aggressive cancer of hematopoietic stem/progenitor cells originating in the bone marrow. Leukemic cells and their microenvironment are diverse, vary across patients, and may be clinically quite relevant. Single-cell RNA sequencing (scRNAseq) is increasingly used to unbiasedly characterize patient-derived AML cells to address a variety of biological questions. Annotations of these cells rely on expert-based curations which are time-consuming and not generalizable. Additionally, AML cells may lay on a continuum and could therefore be a mixture of categories, making it challenging to annotate specifically in-between cell types. There is thus an important need for an accurate and automated method to annotate the diverse cell types of normal hematopoietic cells and in AML samples. 

## Methodology

*Method Overview*
![Process Overview](Model/img/fig1.png)


We tested different machine learning algorithms using re-processed raw data from Human Cell Atlas (HCA) bone marrow scRNA-seq dataset (8 donors, 220K cells). An extensive annotation effort was deployed using both previous annotations[^1][^2] released for this dataset (each with strengths and limitations) and expert curation by our group. A subset of the reannotated cohort was used for training and validation of machine learning algorithms. Artificial neural network (ANN) offered an excellent accuracy and was selected. Its performance was validated on an independent AML dataset comprising 15K curated AML cells[^7]. With our classifier, users don’t need to provide an annotated reference, as with other published algorithms (e.g., Seurat Label transfer). A tutorial for using this classifier is provided within Jupyter notebooks for both R (Seurat) and Python (Scanpy) test datasets.


*Process Flowchart*
![Process Flowchart](Model/img/fig2.png)

### Training Data
From the Human Cell Atlas (HCA), we took the 10X single cell RNA-seq FASTQ files from immune cell atlas of human hematopoietic system[^4]. There are 284 FASTQ files from 8 donors, 9 flow cells per donor in this reference. ***To Banafsheh: I would suggest to include a file manifest of the input data file used***

### Reprocessing of HCA data 
Each flow cell sample was run through Cell Ranger V5[^5], which produces filtered/unfiltered matrix of gene counts per cell for each sample. We used the filtered matrices to create 72 datasets (one per sample). These were combined, normalized to create one final dataset containing cells from all the donors and flow cells (226148 cells). Annotation from Hay et. al[^1] and DISCO[^2] paper were added to cells. However, they don’t cover all the cells, due to difference of Cell Ranger pipelines and reference genomes used.

#### Filtering & integration:
We first filtered cells with high percentage of Mitochondrial rna which removed 7992 cells, leaving in 218156 cells. Doublet scores was calculated using scDblFinder package[^6].

We then created a UMAP embeddings of data and divided the cells into five groups: main cluster (will cells from HSC-like to myeloid and Erythroblast, T/NK cells, B cells, plasma and Stromal cells) ***To Banafsheh: Review this sentence, I don't understand.  Did you divide the cells into 5 main clusters and performed filtering within cluster?  Or did you further clustered the cells within each of these 5 groups?***.  For each group, we removed clusters with doublet scores well above the rest of cells, clusters with `mean(log(doublet score)) >= 1`  ***To Banafsheh: I assume that's what you meant here?***. We also manually explored cluster with feature levels well below the rest (<= 630 features), and for these clusters, the decision was made to either keep, removed the whole cluster or remove cells with less than 500 features. We end up with 195414 high quality cells, which were, then, integrated using Harmony[^3].

#### Cell type Annotation:
Annotation of HSC-like, progenitors, plasma, stromal and platelet cell types were taken from Hay et. al[^1] and the rest of cell-types were taken from DISCO[^2] to acquire the desired granularity. ***To Banafsheh: Perhaps add a bit of rational here to explain this annotation scheme for people who don't know much about these 2 references*** Any cells from DISCO that was in Hay et.al group was temporarily assigned to `NA`, this resulted in 137707 annotated cells and 57702 NA cells. 

**Cell types annotation source**
![CellType Annotation Reference](Model/img/fig3.png)

To recover `NA` cells, we leveraged annotation from their nearest neighbors.  We tested values of different values of K ( hyperparameter representing number of nearest neighbors to use) for finding the optimal number of neighbors.
For testing the optimal K to use, we randomly assigned at least 10% of cells per cell-type to NA and used their (K= 3 to 25) neighbors to predict their cell-type. Cells that did not have any annotated neighbor, were annotated based on the most abundant cell-type present in their cluster. We iterated this process 25 times and choose K=15 as the  optimal K.  We annotated 53633 `NA` cells and the remaining  4069 `NA` cells were annotated based on their cluster’s most abundant cell-type.


#### Final processing and reference data for classifier:
We, then explored each cell-type individually and did a second round of filtering of cells (Megakaryocytes, pre-pDC, Intermediate EPCAM+ erythroblast, and Monocyte MHCIIHigh) that were clearly outside of their main clusters. This resulted in 195020 cells in our final object. We then created a subset object with 3000 cells per cell-types except for T/NK/Erythro cells where we picked around 750 cells per each of their sub categories (64326 cells). We also identified some cells in-between monocyte and cDC2 cells, which for now we have classified as MoDC.  This is the final labeled dataset used for training the classifier.  ***To Banafsheh: Did you keep the rest as your test set?***

**Final cell type annotation**
![Screenshot](Model/img/fig4.png)


### Training of the classifier
We used artificial neural network algorithm as it does a great job learning cell-types based on gene-expressions. ANN was trained using various parameters in our reference dataset containing 64326 cells and 36601 genes and it was able to recognize our desired cell-types. Our current ANN version is developed using the MLPClassifier package of the scikit-learn[^8] python library, with 350 hidden layers, RelU activation, stochastic gradient descent weight optimization. The classifier model is named `ANNClassifier.sav` and the features used for it is in `ANNClassifier_features.txt` ***To Banafsheh:  Have you optimized the hyperparameters?***    

***To Banafsheh: I think we are missing a results section here, with some validation performance metrics and figures (it was mentionned in the abstract section that you validated on the van Galen dataset).  If this activity is still ongoing, perhaps state that and what is the plan to verify the classifier performance (will you use test separately on AML cells and normal cells, etc...)***

## Results

### Applying the classifier
Jupyter notebooks (in R and Python) were prepared as simple tutorials for loading the classifier and applying it to test datasets.

***To Banafsheh: Perhaps state what is the required pre-processing for the data.  Does it need to be 10x 3' sequencing data.  Does it require a particular Cell Ranger version?  The notebook show the LogNormalization step, but it should probably be explicitely stated here as well***


## Authors / Credits
***In a lab github repo, its a good practice to add main contributors to each project. To Banafsheh: Add other contributors***

- [Banafsheh Khakipoor](https://github.com/BanafshehKhaki)



[^1]: Hay, Stuart B et al. “The Human Cell Atlas bone marrow single-cell interactive web portal.” Experimental hematology vol. 68 (2018): 51-61. doi:10.1016/j.exphem.2018.09.004

[^2]: Li, Mengwei et al. “DISCO: a database of Deeply Integrated human Single-Cell Omics data.” Nucleic acids research vol. 50,D1 (2022): D596-D602. doi:10.1093/nar/gkab1020

[^3]: Korsunsky, Ilya et al. “Fast, sensitive and accurate integration of single-cell data with Harmony.” Nature methods vol. 16,12 (2019): 1289-1296. doi:10.1038/s41592-019-0619-0

[^4]: https://explore.data.humancellatlas.org/projects/cc95ff89-2e68-4a08-a234-480eca21ce79

[^5]: https://support.10xgenomics.com/single-cell-gene-expression/software/pipelines/5.0/algorithms/overview

[^6]: https://bioconductor.org/packages/devel/bioc/vignettes/scDblFinder/inst/doc/scDblFinder.html

[^7]: van Galen, Peter et al. “Single-Cell RNA-Seq Reveals AML Hierarchies Relevant to Disease Progression and Immunity.” Cell vol. 176,6 (2019): 1265-1281.e24. doi:10.1016/j.cell.2019.01.031

[^8]: Pedregosa et al. "Scikit-learn: Machine Learning in Python", JMLR 12, pp. 2825-2830, 2011.
