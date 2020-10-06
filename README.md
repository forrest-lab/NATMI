# NATMI: Network Analysis Toolkit for the Multicellular Interactions

Recent development of high throughput single-cell sequencing technologies has made it cost-effective to profile thousands of cells from a complex sample. Examining ligand and receptor expression patterns in the cell types identified from these datasets allows prediction of cell-to-cell communication at the level of niches, tissues and organism-wide. Here, we developed NATMI (Network Analysis Toolkit for Multicellular Interactions), a Python-based toolkit for multi-cellular communication network construction and network analysis of multispecies single-cell and bulk gene expression and proteomic data. 
<details>
<summary>Read more...</summary>

NATMI uses connectomeDB2020 (the most up-to-date manually curated ligand-receptor interaction list) and user supplied gene expression tables with cell type labels to predict and visualize cell-to-cell communication networks. By interrogating the Tabula Muris cell atlas we demonstrate the utility of NATMI to identify cellular communities and the key ligands and receptors involved. Notably, we confirm our previous predictions from bulk data that autocrine signalling is a major feature of cell-to-cell communication networks and for the first time ever show a substantial potential for self-signalling of individual cells through hundreds of co-expressed ligand-receptor pairs. Lastly, we identify age related changes in intercellular communication between the mammary gland of 3 and 18-month-old mice in the Tabula Muris dataset. NATMI and our updated ligand-receptor lists are freely available to the research community.
</details>  

How to cite: 
1. Hou, R., Denisenko, E., Ong, H.T. et al. Predicting cell-to-cell communication networks using NATMI. Nat Commun 11, 5011 (2020). https://doi.org/10.1038/s41467-020-18873-z
2. Ramilowski, J., Goldberg, T., Harshbarger, J. et al. A draft network of ligand–receptor-mediated multicellular signalling in human. Nat Commun 6, 7866 (2015). https://doi.org/10.1038/ncomms8866

Contact: Rui Hou [rui.hou@research.uwa.edu.au]

## Table of Contents
1. [**About NATMI**](#1)
2. [**Download and Installation**](#2)
3. [**Required Data and Formats**](#3)
   1. [Supported Species and IDs](#3.1)
   2. [Ligand-Receptor Interactions (connectomeDB2020)](#3.2)
   3. [Ligand-Receptor Interactions (user-supplied interactions)](#3.3)
   4. [Expression Data](#3.4)
   5. [Cell Labels Metafile (single-cell analysis only)](#3.5)
4. [**Command Line Utilities**](#4)
   1. [ExtractEdges.py](#4.1)
   2. [DiffEdges.py](#4.2)
   3. [VisInteractions.py](#4.3)
5. [**Example Workflow Simple (single-cell toy dataset)**](#5)
   1. [Extract ligand-receptor-mediated interactions](#5.1)
   2. [Visualise cell-to-cell communication networks](#5.2)
6. [**Example Workflow Advanced (Tabula Muris Senis dataset)**](#6)
   1. [Extract ligand-receptor-mediated interactions at two time-points.](#6.1)
   2. [Identify variations in cell-to-cell signaling networks](#6.2)
   3. [Visualize the cell-to-cell communication networks (Figure 6 of the manuscript)](#6.3)
7. [**Frequently Asked Questions**](#faq)

## 1. About NATMI <a name="1"></a>

**NATMI** is fast, flexible and easy-to-use tool to construct **cell-to-cell communication networks** from user-supplied **multi-omics data** (single-cell and bulk) in a **variety of species**. 

NATMI is: **python-based** ([software requirements](#2)) and is built on [**connectomeDB2020**](#3.2) (default). Users can also add and interrogate their **own interactions** in **any species** or explore existing [Tabula Muris](https://tabula-muris.ds.czbiohub.org/), [Tabula Muris Senis](https://tabula-muris-senis.ds.czbiohub.org/) and [FANTOM5 cell atlas](http://fantom.gsc.riken.jp/5/suppl/Ramilowski_et_al_2015/) datastets. 

*Developed and maintained by Rui Hou [rui.hou@research.uwa.edu.au] at the laboratory of Professor Alistair Forrest [alistair.forrest@perkins.uwa.edu.au] at the Harry Perkins Institute of Medical Research.*

## 2. Download and Installation [(top)](#table-of-contents) <a name="2"></a>

To use NATMI, following software is required: 

- Python 2 or Python 3

- Python libraries [pandas](https://pandas.pydata.org/), [XlsxWriter](https://xlsxwriter.readthedocs.io/index.html) and [xlrd](https://xlrd.readthedocs.io/en/latest/) (for general data processing).

- Python libraries [seaborn](https://seaborn.pydata.org/), [igraph](https://igraph.org/python/), [NetworkX](https://networkx.github.io/), [PyGraphviz](https://pygraphviz.github.io/), [bokeh](https://docs.bokeh.org/en/latest/) and [holoviews](https://holoviews.org/index.html) (for cell-to-cell communication network visualization).

*NATMI was tested using **python 2.7.17 version** with pandas 0.24.2, XlsxWriter 1.2.8, xlrd 1.2.0, seaborn 0.9.0 , igraph 0.7.1, NetworkX 2.2 and PyGraphviz 1.3.1 and **python 3.7.6 version** with pandas 1.0.3, XlsxWriter 1.2.8, xlrd 1.2.0, seaborn 0.10.1 , igraph 0.7.1, NetworkX 2.4, PyGraphviz 1.5, bokeh 2.0.2 and holoviews 1.13.2.*

To install NATMI, run the following command in the desired installation directory:
```bat
   git clone https://github.com/asrhou/NATMI.git
```

This tool currently provides command-line utilities only.

## **3. Required Data and Formats** [(top)](#table-of-contents) <a name="3"></a>

To explore cell-to-cell communication NATMI uses: 

   1. **ligand-receptor interactions** ([precompiled connectomeDB2020](#3.2) or [user-supplied binary pairs](#3.3)), 

   2. [**user-supplied gene/protein abundance data**](#3.4), and 

   3. [**the metafile describing mapping between each cell and a cell-type label**](#3.5) across the whole dataset (single-cell analysis only). 

Detailed requirements are described as follows. 

### 3.1 Supported Species and IDs <a name="3.1"></a>

By **default** NATMI uses [connectomeDB2020](#3.2) human ligand-receptor interactions, but using homologs of interacting pairs it can support a total of **21 different species** including additional species such as mouse, rat, zebrafish, etc. [(NCBI HomoloGene Database)](https://www.ncbi.nlm.nih.gov/homologene/statistics/). All supported species can be listed by running ExtractEdges.py with '-h' argument and then a species of interest can be specified by using '--species [species_name]' argument. 

For the supported species, NATMI generally requires to provide **official gene symbols** in the **user-supplied gene/protein abundance data**. For human and mouse, additional identifiers are supported: **[HGNC ID](https://www.genenames.org/), [MGI ID](http://www.informatics.jax.org/mgihome/nomen/index.shtml), [Entrez gene ID](https://www.ncbi.nlm.nih.gov/gene), [Ensembl gene ID](https://www.ensembl.org/), [UniProt ID](https://www.uniprot.org/)**, which are then converted to gene symbols using [HGNC](https://www.genenames.org/download/statistics-and-files/) and [MGI](http://www.informatics.jax.org/downloads/reports/index.html) ID mapping files. *If multiple human/mouse IDs are associated with the same gene symbol, their expression levels are summed up as the total expression level of the corresponding gene symbol.*

For **user-supplied interactions**, NATMI can work with **any species** and **any IDs** ([as described](#3.3)).  

### 3.2 Ligand-Receptor Interactions (connectomeDB2020) <a name="3.2"></a>

As of 2020, **connectomeDB2020** is the most up-to-date curated database of **2,187 human ligand-receptor interactions with primary literature support (in the current manuscript)** and additional **1,791 putative pairs (available as a part of NATMI only)**, which builds on our previous draft and a database of human cell interactions ([Ramilowski, J. A., et al.  Nat Commun 6, 7866 (2015)](https://www.nature.com/articles/ncomms8866)). By default, *ExtractEdges.py* of NATMI extracts edges from input expression data based on the literature-supported ligand-receptor pairs from **connectomeDB2020**. For non-human supported species, NATIM only extracts their human homologs from [NCBI HomoloGene Database](https://www.ncbi.nlm.nih.gov/homologene/).

**Note:** *1.* A web interface to present the connectomeDB2020 database is is available at https://asrhou.github.io/NATMI/. *2.* Since some of the reported ligand-receptor pairs in connectomeDB2020 might be human specific only, always verify if a given edge is valid for your analysed species.

### 3.3 Ligand-Receptor Interactions (user-supplied interactions) <a name="3.3"></a>

To allow flexibility, NATMI can also work with **user-supplied ligand-receptor interactions** (argument '--interDB') to construct and visualize network of interactions including but not limited to connectomeDB2020. This option can be particularly useful for users who wish to expand the list of our default interactions, explore their own (species-specific) interactions and/or explore cell-to-cell communication in other than [the 21 default species](#3.1). Here, we briefly describe **required formats** for an interaction data file. 

Similarly to the precompiled connectomeDB2020 datasets, an interaction data file must be stored in the 'lrdbs' folder in one of the following formats: csv, tsv, txt, xls or xlsx under a desired name which should be unique in the folder without extension (used to specify the argument '--interDB'). The interaction data should be represented as a two-column table, with the **first column** containing **ligands** and the **second column** containing **receptors** (as in the following example). 

|Ligand|Receptor|
|-:|:-|
|LIGAND1|RECEPTOR1|
|LIGAND2|RECEPTOR2|
|LIGAND3|RECEPTOR2|
|LIGAND4|RECEPTOR3|
|LIGAND4|RECEPTOR4|
|...|...|...|

The **IDs** of these ligands and receptors can be of **any format** depending on the uasge scenario as will be further described.

<a name="3.3.1"></a>
#### connectomeDB2020-like Format

If provided ligands-receptors pairs are represented by **human or mouse gene symbols** and the user-specified expression data are [from supported species using supported IDs](#3.1), NATMI will extract edges similarly to [the connectomeDB2020 workflow](#5). This functionality could be potentially useful for the users who want to add additional human interactions to the existing connectomeDB ligand-receptor pairs (by setting argument **'--interSpecies'** to **'expandp'** or **'expanda'**) and explore the supported species. In addition to expanding connectomeDB2020, the user can also divide the list based on the type of ligands. In this way, the networks of [juxtacrine](https://en.wikipedia.org/wiki/Juxtacrine_signalling), [paracrine](https://en.wikipedia.org/wiki/Paracrine_signaling) and [endocrine](https://en.wiktionary.org/wiki/endocrine) can be studied separately. 

<a name="3.3.2"></a>
#### Customised Format

NATMI can also potentially construct and visualize networks of any type of binary interaction data, helping users to explore
1. expression data from **unsupported species**, 
2. expression data with **unsupported IDs**, and/or 
3. user-supplied ligand-receptor interactions with **gene IDs other than human and mouse gene symbols**.  

This can be achieved by setting the argument **'--idType'** to **'custom'**. NATMI will then skip gene ID conversion and gene homology matching to construction cell-to-cell communication networks of binary interactions from the user-supplied data directly. 

Users should make sure that, when the argument **'--idType'** is set to **'custom'**, input **customised interaction file** and **expression data** share the **common ID type**. In all output files, **original IDs** are preserved. 

### 3.4 Expression Data <a name="3.4"></a>

User-specified gene/protein abundance matrix files should be in the following formats: csv, tsv, txt, xls or xlsx. And for the default usage with [connectomeDB2020](#3.2), it is required that the gene/protein IDs are in one of the following formats: official gene symbols (default) or human HGNC IDs, mouse MGI IDs, or human and mouse Entrez gene IDs, Ensembl gene IDs, and UniProt IDs (see [Supported IDs](#3.1)). **For multiple human/mouse IDs associated with the same gene symbol, their expression levels are summed up as the total expression level of the corresponding gene symbol**. Each column is a normalised gene/protein expression profile of a cell type or an individual cell. An example snapshot of the abundance matrix is shown below.

||Sample1|Sample2|Sample3|...|
|-:|:-:|:-:|:-:|:-|
|**Gene1**|23|0|958|...|
|**Gene2**|1555|1.2|9.9|...|
|**Gene3**|0|658.01|0|...|
|...|...|...|...|...|

For **user-supplied interactions in [customised format](#3.3.2)**, the IDs in gene/protein abundance matrix can be of any format matching the interactions. Additionally, [Tabula Muris](https://tabula-muris.ds.czbiohub.org/), [Tabula Muris Senis](https://tabula-muris-senis.ds.czbiohub.org/) and [FANTOM5 cell atlas](http://fantom.gsc.riken.jp/5/suppl/Ramilowski_et_al_2015/) can also be explored. 

### 3.5 Cell Labels Metafile (single-cell analysis only) <a name="3.5"></a>

For single-cell gene expression data, the user needs to provide a metafile with the mapping between each cell in the dataset and a cell-type label. It also should be saved in the following formats: csv, tsv, txt, xls or xlsx. Following table displays an example metafile.

|Cell|Annotation|
|-:|:-|
|Barcode1|Cell-type1|
|Barcode2|Cell-type1|
|Barcode3|Cell-type2|
|...|...|

## 4. Command Line Utilities [(top)](#table-of-contents) <a name="4"></a>

NATMI is a python-based tool (see [software requirements](#2)) to construct cell-to-cell ligand-receptor-mediated communication networks from multi-omics data. It works with user-specified (gene/protein) abundance matrix files or can be used to explore Tabula Muris, Tabula Muris Senis and FANTOM5 cell atlas (see [required data](#3.4)). 

NATMI scipts can be executed from the installation directory directly (or from any directory if their absolute path is specified) using following commands:

<a name="4.1"></a>
### 4.1 ExtractEdges: Extracting ligand-receptor-mediated interactions between cell types in the input gene/protein abundance data.

*Optional arguments are enclosed in square brackets […]*

```
python ExtractEdges.py [-h] [--interDB INTERDB] [--interSpecies INTERSPECIES] --emFile EMFILE [--annFile ANNFILE] [--species SPECIES] [--idType IDTYPE] [--coreNum CORENUM] [--out OUT]

Arguments:
  -h, --help            show this help message and exit
  --interDB INTERDB
                        lrc2p (default) has literature supported ligand-receptor pairs | lrc2a has putative and literature supported ligand-receptor pairs | the user-supplied interaction database can also be used by calling the name of database file without extension
  --interSpecies INTERSPECIES
                        human (default) | mouse | expandp | expanda
  --emFile EMFILE       the path to the normalised expression matrix file with row names (gene identifiers) and column names (cell-type/single-cell identifiers)
  --annFile ANNFILE     the path to the metafile in which column one has single-cell identifiers and column two has corresponding cluster IDs (see file 'toy.sc.ann.txt' as an example)
  --species SPECIES     human (default) | mouse | rat | zebrafish | fruitfly | chimpanzee | dog | monkey | cattle | chicken | frog | mosquito | nematode | thalecress | rice | riceblastfungus | bakeryeast | neurosporacrassa | fissionyeast | eremotheciumgossypii | kluyveromyceslactis, 21 species are supported
  --idType IDTYPE       symbol (default) | entrez(https://www.ncbi.nlm.nih.gov/gene) | ensembl(https://www.ensembl.org/) | uniprot(https://www.uniprot.org/) | hgnc(https://www.genenames.org/) | mgi(http://www.informatics.jax.org/mgihome/nomen/index.shtml) | custom(gene identifier used in the expression matrix)
  --coreNum CORENUM     the number of CPU cores used, default is one
  --out OUT             the path to save the analysis results
```

**Note**: Normalised expression matrix, metafile and ligand-receptor interaction database files are supported in csv, tsv, txt, xls or xlsx format.

Predict ligand-receptor-mediated interactions in a mouse single-cell RNA-seq dataset using literature supported ligand-receptor pairs and four CPUs:
```bat
   python ExtractEdges.py --species mouse --emFile toy.sc.em.txt --annFile toy.sc.ann.txt --interDB lrc2p --coreNum 4
```

Predict ligand-receptor-mediated interactions in a human bulk RNA-seq dataset using putative and literature supported ligand-receptor pairs and one CPU:
```bat
   python ExtractEdges.py --species human --emFile toy.bulk.em.xls --interDB lrc2a
```

ExtractEdges.py creates a folder using the name of the expression matrix or the user specified name. README.txt (in the output folder) contains information about other files in the folder.

<a name="4.2"></a>
### 4.2 DiffEdges: Identification of changes in ligand-receptor edge weights between a cell-type pair in two conditions. 
*Optional arguments are enclosed in square brackets […]*

**Note**: Only weight changes across two condition from the same, or similar, datasets and with the same 'interDB' of ligand-receptor pairs (literature-supported with literature-supported or all with all) should be compared.

```
python DiffEdges.py [-h] --refFolder REFFOLDER --targetFolder TARGETFOLDER [--interDB INTERDB] [--weightType WEIGHTTYPE] [--out OUT]

Arguments:
  -h, --help            show this help message and exit
  --refFolder REFFOLDER
                        the path to the folder of the reference dataset
  --targetFolder TARGETFOLDER
                        the path to the folder of the target dataset
  --interDB INTERDB
                        lrc2p (default) | lrc2a | the name of the ligand-receptor interaction database file without extension
  --weightType WEIGHTTYPE
                        mean (default) | sum
  --out OUT
                        the path to save the analysis results
```

Detect changes in edge weight in two output folders (generated by ExtractEdges.py) using literature supported ligand-receptor pairs:
```bat
   python DiffEdges.py --refFolder /path/to/ExtractEdges.py's/output/folder/of/reference/dataset --targetFolder /path/to/ExtractEdges.py's/output/folder/of/target/dataset --interDB lrc2p
```

DiffEdges.py creates a folder from the names of the two datasets or the user specified name. README.txt (in the output folder) contains information about other files in the folder.

<a name="4.3"></a>
### 4.3 VisInteractions.py: Visualisation of the network analysis results from ExtractEdges.py and DiffEdges.py.
*Optional arguments are enclosed in square brackets […]*

```
python VisInteractions.py [-h] --sourceFolder SOURCEFOLDER [--interDB INTERDB] [--weightType WEIGHTTYPE] [--specificityThreshold SPECIFICITYTHRESHOLD]
                          [--expressionThreshold EXPRESSIONTHRESHOLD] [--detectionThreshold DETECTIONTHRESHOLD]
                          [--keepTopEdge KEEPTOPEDGE] [--plotWidth PLOTWIDTH] [--plotHeight PLOTHEIGHT] [--plotFormat PLOTFORMAT]
                          [--edgeWidth EDGEWIDTH] [--clusterDistance CLUSTERDISTANCE] [--drawClusterPair DRAWCLUSTERPAIR]
                          [--layout LAYOUT] [--fontSize FONTSIZE] [--maxClusterSize MAXCLUSTERSIZE]
                          [--drawNetwork DRAWNETWORK] [--drawLRNetwork [LIGAND [RECEPTOR ...]]]

Arguments:
  -h, --help            show this help message and exit
  --sourceFolder SOURCEFOLDER
                        the path to the folder of extracted edges from ExtractEdges.py or DiffEdges.py
  --interDB INTERDB
                        lrc2p (default) | lrc2a | the name of the ligand-receptor interaction database file without extension
  --weightType WEIGHTTYPE
                        mean (default) | sum, method to calculate the expression level of a ligand/receptor in a cell type
  --specificityThreshold SPECIFICITYTHRESHOLD
                        do not draw the edges whose specificities are not greater than the threshold (default 0).
  --expressionThreshold EXPRESSIONTHRESHOLD
                        do not draw the edges in which expression levels of the ligand and the receptor are not greater than the threshold (default 0).
  --detectionThreshold DETECTIONTHRESHOLD
                        do not draw the interactions in which detection rates of the ligand and the receptor are lower than the threshold (default 0.2).
  --keepTopEdge KEEPTOPEDGE
                        only draw top n interactions that passed the thresholds (default 0 means all interactions that passed the thresholds will be drawn).
  --plotWidth PLOTWIDTH
                        resulting plot's width (default 12).
  --plotHeight PLOTHEIGHT
                        resulting plot's height (default 10).
  --plotFormat PLOTFORMAT
                        pdf (default) | png | svg, format of the resulting plot(s)
  --edgeWidth EDGEWIDTH
                        maximum thickness of edges in the plot (default 0: edge weight is shown as a label around the edge).
  --clusterDistance CLUSTERDISTANCE
                        relative distance between clusters (default value is 1; if clusterDistance >1, the distance will be increased, if clusterDistance >0 and clusterDistance <1, the distance will be decreased).
  --drawClusterPair DRAWCLUSTERPAIR
                        n(o) (default) | y(es)
  --layout LAYOUT       kk (default) | circle | random | sphere; 'kk' stands for Kamada-Kawai force-directed algorithm
  --fontSize FONTSIZE   font size for node labels (default 8).
  --maxClusterSize MAXCLUSTERSIZE
                        maximum radius of the clusters (default 0: all clusters have identical radius).
  --drawNetwork DRAWNETWORK
                        y(es) (default) | n(o)
  --drawLRNetwork [DRAWLRNETWORK [DRAWLRNETWORK ...]]
                        ligand and receptor's symbols
```


Visualise cell-connectivity-summary networks from the results of ExtractEdges.py or DiffEdges.py:

```bat
   python VisInteractions.py --sourceFolder /path/to/result/folder --interDB lrc2p --weightType mean --detectionThreshold 0.2 --plotFormat pdf --drawNetwork y --plotWidth 12 --plotHeight 10 --layout kk --fontSize 8 --edgeWidth 0 --maxClusterSize 0 --clusterDistance 1
```

If run on the output of ExtractEdges.py, VisInteractions.py creates a new folder in the output folder of ExtractEdges.py containing networks with three different weights. If run on the output of DiffEdges.py, VisInteractions.py creates a new folder in the output folder of DiffEdges.py, containing networks with three different weights in reference and target datasets. Additionally, delta networks are drawn, where yellow edges are (non-significant) edges with the fold change of their weights in two conditions of two or less. For other edges, a red color indicates the edges with a weight higher in the reference dataset, and a green color indicates the edges with a weight higher in the target dataset. The color intensity scales with the degree of change.

Visualise cell-to-cell communication networks between all possible pairs of cell types using results of ExtractEdges.py or DiffEdges.py:

```bat
   python VisInteractions.py --sourceFolder /path/to/result/folder --interDB lrc2p --drawClusterPair y
```

If run on the output of ExtractEdges.py, VisInteractions.py creates a new folder in the output folder of ExtractEdges.py containing bipartite graphs with three different weights. If run on the output of DiffEdges.py, VisInteractions.py creates a new folder in the output folder of DiffEdges.py, containing four kinds of interactions. From a cell type to another cell type, each kind of interactions form a separate bipartite graph.

Visualise cell-to-cell communication networks via a ligand-receptor pair from the results of ExtractEdges.py:

```bat
   python VisInteractions.py --sourceFolder /path/to/result/folder --interDB lrc2p --drawLRNetwork LIGAND.SYMBOL RECEPTOR.SYMBOL
```

If run on the output of ExtractEdges.py, VisInteractions.py creates a new folder in the output folder of ExtractEdges.py containing the simple graph and hypergraph for the given ligand-receptor pair in the dataset. 

## 5. Example Workflow Simple (single-cell toy dataset) [(top)](#table-of-contents) <a name="5"></a>
This workflow shows how to extract and visualize intercellular communication using mouse single-cell RNA-seq dataset ('toy.sc.em.txt') and the corresponding annotation file ('toy.sc.ann.txt') and literature supported ligand-receptor pairs from connectomeDB2020. 

**Note**: All results of following commands can be found in 'example' folder.

<a name="5.1"></a>
### 5.1 Extract ligand-receptor-mediated interactions in 'toy.sc.em.txt' and save results to 'example' folder using ExtractEdges.py. 
For each analysis, NATMI always starts from predicting potential ligand-receptor-mediated interactions between cells using the user-specified ligand-receptor pairs. Here, we use *ExtractEdges.py* to extract interactions in the toy single-cell dataset (with three cell types) based on literature supported ligand-receptor pairs from the **connectomeDB2020**.

```bat
   python ExtractEdges.py --species mouse --emFile toy.sc.em.txt --annFile toy.sc.ann.txt --interDB lrc2p --coreNum 4 --out example
```

<a name="5.2"></a>
### 5.2 Visualise ligand-receptor-mediated interaction network of in 'toy.sc.em.txt' in three different ways. 
The output of ExtractEdges.py in the *'example'* folder are the predicted edges between the three cell types. Visualisation of these extracted edges is a good place to start interrogating their biological meaning. For a complete view of the cell-to-cell communication network, we first visualise the cell-connectivity-summary network in the *'example'* folder.

```bat
   python VisInteractions.py --sourceFolder example --interDB lrc2p --weightType mean --detectionThreshold 0.2 --drawNetwork y --plotWidth 4 --plotHeight 4 --layout circle --fontSize 15 --edgeWidth 6 --maxClusterSize 0 --clusterDistance 0.6
```

Network in *example/Network_exp_0_spe_0_det_0.2_top_0_signal_lrc2p_weight_mean/network_total-specificity-based_layout_circle.pdf* is the cell-connectivity-summary network weighted by the sum of specificity weights for ligand-receptor pairs with common sending and receiving cell types. Apparently, there are more specific ligand-receptor pairs from endothelial cell to itself. 

We then visualise top 15 ligand-receptor pairs between the three cell types.

```bat
   python VisInteractions.py --sourceFolder example --drawClusterPair y --keepTopEdge 15
```

Bipartite graph in *example/CltPair_exp_0_spe_0_det_0.2_top_15_signal_lrc2p_weight_mean/From_Endothelial Cells_to_Endothelial Cells_spe.pdf* shows 15 most specific ligand-receptor pairs endothelial cell to itself. Based on the edge weights (specificities), 11 of these are only detected in endothelial cells. Since the specificity of Efnb2-Pecam1 pair in the graph is less than one, it is interesting to know which cell-type pairs are also connected by Efnb2-Pecam1 pair.

We thus visualise the cell-to-cell communication network via Efnb2-Pecam1 pair.

```bat
   python VisInteractions.py --sourceFolder example --drawLRNetwork Efnb2 Pecam1 --plotWidth 4 --plotHeight 4 --layout circle --fontSize 15 --edgeWidth 6 --maxClusterSize 0 --clusterDistance 0.6
```

Network in *example/LRNetwork_Efnb2-Pecam1_exp_0_spe_0_det_0.2_top_0_signal_lrc2p_weight_mean/network_Efnb2-Pecam1_layout_circle.pdf* only has one edge. This means although other cell-type pairs are connected by edges of Efnb2-Pecam1 pair, only for endothelial cell, Efnb2 and Pecam1 are detected in > 20 % cells. Therefore, Efnb2-Pecam1 pair is only reliably detected in endothelial cell.

## 6. Example Workflow Advanced (Tabula Muris Senis dataset) [(top)](#table-of-contents) <a name="6"></a>

To demonstrate the usage of NATMI in delta network analysis, we show the analysis on Tabula Muris Senis (as in our manuscript). **Processed Tabula Muris Senis data 'Mammary_Gland_droplet.h5ad' was first downloaded from figshare (https://figshare.com/projects/Tabula_Muris_Senis/64982)**. We then extracted 3 and 18-month-old mammary gland cells and normalized each expression profile by dividing it by the total number of unique molecular identifiers and multiplying by 1,000,000. Such normalized gene expression data and annotations are available in figshare: https://figshare.com/s/7f45bf6352da453b3266.

<a name="6.1"></a>
### 6.1 We first extract edges between cells of the 3- and 18-month-old mammary glands in mice using ExtractEdges.py. 
```bat
   python ExtractEdges.py --species mouse --emFile /path/to/3m.upm.em.csv --annFile /path/to/3m.ann.csv --interDB lrc2p --coreNum 4 --out 3m.mg

   python ExtractEdges.py --species mouse --emFile /path/to/18m.upm.em.csv --annFile /path/to/18m.ann.csv --interDB lrc2p --coreNum 4 --out 18m.mg
```
<a name="6.2"></a>
### 6.2 The variations in cell-to-cell signaling between 3- and 18-month-old murine mammary glands are then identified by DiffEdges.py.

```bat
   python DiffEdges.py --refFolder 3m.mg --targetFolder 18m.mg --interDB lrc2p --out 3m-18m
```

<a name="6.3"></a>
### 6.3 We visualize up- and down-regulated edges between 3 months and 18 months using VisInteractions.py as in Figure 6 of the manuscript.

```bat
   python VisInteractions.py --sourceFolder 3m-18m --interDB lrc2p --weightType mean --detectionThreshold 0.2 --drawNetwork y --plotWidth 10 --plotHeight 10 --layout circle --fontSize 15 --edgeWidth 6 --maxClusterSize 0 --clusterDistance 0.6
```

*Resulting networks are in the folder '/path/to/3m-18m/Delta_Network_exp_0_spe_0_det_0.2_top_0_signal_lrc2p_weight_mean'*

## 7. Frequently Asked Questions [(top)](#table-of-contents) <a name="faq"></a>
Not sure how to best use NATMI? Please check our manual above and/or read through this FAQ section. If you are still not finding your answers, send us an email: [rui.hou@research.uwa.edu.au]

**This section will be expanded when NATMI becomes publicly available.**

:question:   I have some mouse-specific interactions, can I adopt NATMI to explore a mouse dataset using these mouse-specific interactions?

:a:: Yes. Please prepare your interaction data in the [required format](#3.3.1). Then run [ExtractEdges.py](#4.1) by setting argument '--interSpecies' to 'mouse'. If these interactions are not represented by mouse gene symbols, please ensure that gene IDs in the interaction file and expression file are matching, and run [ExtractEdges.py](#4.1) by setting argument '--idType' to 'custom'. A complete analysis workflow can be found [here](#5.1).

:question:   We found some new human ligand-receptor interactions, is it possible to add them to connectomeDB2020 but keep them in a separate interaction file when we use NATMI?

:a:: Yes. When you run [ExtractEdges.py](#4.1), in addition to set argument '--interDB' to your interaction file name without extension, please set the argument '--interSpecies' to 'expandp' or 'expanda', NATMI will automatically combine your interactions with 'lrc2p' (literature supported ligand-receptor pairs in connectomeDB2020) or 'lrc2a' (literature supported and putative ligand-receptor pairs in connectomeDB2020) database and uses the combined pair list to extract ligand-receptor-mediated edges. Moreover, if '--interSpecies' is set to 'human', NATMI only uses your interactions to build the cell-to-cell communication network.

