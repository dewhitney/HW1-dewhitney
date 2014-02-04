BIOST 578, HW1
========================================================

**This is your first homework due Tuesday Feb 4.**

1. Use the GEOmetabd package to find all HCV gene expression data using the Illumina platform submitted by an investigator at Yale. This should be done with a single query, showing the title, the GSE accession number, the GPL accession number and the manufacturer and the description of the platform used.

**Answer**First we update and install the necessary packages for Bioconductor.

```r
# This can take a long time to download/install
source("http://bioconductor.org/biocLite.R")
```

```
## Bioconductor version 2.13 (BiocInstaller 1.12.0), ?biocLite for help
```

```r
biocLite(c("GEOmetadb", "GEOquery"))
```

```
## BioC_mirror: http://bioconductor.org
## Using Bioconductor version 2.13 (BiocInstaller 1.12.0), R version 3.0.2.
## Installing package(s) 'GEOmetadb' 'GEOquery'
```

```
## Warning: packages 'GEOmetadb', 'GEOquery' are in use and will not be installed
## Warning: installed directory not writable, cannot update packages 'doParallel',
##   'foreign', 'lattice', 'Matrix', 'mgcv', 'nlme', 'rpart', 'survival'
```

```r
library(GEOmetadb)

# This database download is particularly slow, so eval=FALSE for this code
# chunk
```


```r
getSQLiteFile()
```



```r
geo_con = dbConnect(SQLite(), "GEOmetadb.sqlite")
```



```r
# Found pubmed_id on the GEO website This query has Illumina and Affymetrix
# manufacturers
res = dbGetQuery(geo_con, "SELECT gpl.title, gpl.gpl, gse.gse, gpl.manufacturer, gpl.description FROM (gse JOIN gse_gpl ON gse.gse = gse_gpl.gse) j JOIN gpl ON j.gpl=gpl.gpl WHERE gse.pubmed_id='23067362'")
```


2. Reproduce your above query using the data.table package. Again, try to use a single line of code. [Hint: You first need to convert all db tables to data.table tables].


```r
library(data.table)
```

