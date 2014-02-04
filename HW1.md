BIOST 578, HW1
========================================================

**This is your first homework due Tuesday Feb 4.**

1. Use the GEOmetabd package to find all HCV gene expression data using the Illumina platform submitted by an investigator at Yale. This should be done with a single query, showing the title, the GSE accession number, the GPL accession number and the manufacturer and the description of the platform used.

First we update and install the necessary packages for Bioconductor.

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
## package 'GEOmetadb' successfully unpacked and MD5 sums checked
## package 'GEOquery' successfully unpacked and MD5 sums checked
## 
## The downloaded binary packages are in
## 	C:\Users\David\AppData\Local\Temp\RtmpOioEUs\downloaded_packages
```

```
## Warning: installed directory not writable, cannot update packages 'doParallel',
##   'foreign', 'lattice', 'Matrix', 'mgcv', 'nlme', 'rpart', 'survival'
```

```r
library(GEOmetadb)
```

```
## Loading required package: GEOquery
## Loading required package: Biobase
## Loading required package: BiocGenerics
## Loading required package: parallel
## 
## Attaching package: 'BiocGenerics'
## 
## The following objects are masked from 'package:parallel':
## 
##     clusterApply, clusterApplyLB, clusterCall, clusterEvalQ,
##     clusterExport, clusterMap, parApply, parCapply, parLapply,
##     parLapplyLB, parRapply, parSapply, parSapplyLB
## 
## The following object is masked from 'package:stats':
## 
##     xtabs
## 
## The following objects are masked from 'package:base':
## 
##     anyDuplicated, append, as.data.frame, as.vector, cbind,
##     colnames, duplicated, eval, evalq, Filter, Find, get,
##     intersect, is.unsorted, lapply, Map, mapply, match, mget,
##     order, paste, pmax, pmax.int, pmin, pmin.int, Position, rank,
##     rbind, Reduce, rep.int, rownames, sapply, setdiff, sort,
##     table, tapply, union, unique, unlist
## 
## Welcome to Bioconductor
## 
##     Vignettes contain introductory material; view with
##     'browseVignettes()'. To cite Bioconductor, see
##     'citation("Biobase")', and for packages 'citation("pkgname")'.
## 
## Setting options('download.file.method.GEOquery'='auto')
## Loading required package: RSQLite
## Loading required package: DBI
```

```r

# This database download is particularly slow, so eval=FALSE for this code
# chunk
getSQLiteFile()
```

```
## Unzipping...
```

```
## Warning: closing unused connection 5
## (http://gbnci.abcc.ncifcrf.gov/geo/GEOmetadb.sqlite.gz)
```

```
## Metadata associate with downloaded file:
##                 name               value
## 1     schema version                 1.0
## 2 creation timestamp 2014-02-01 20:07:10
```

```
## [1] "C:/Users/David/Documents/GitHub/HW1-dewhitney/GEOmetadb.sqlite"
```



```r
geo_con = dbConnect(SQLite(), "GEOmetadb.sqlite")
```

```
## Error: could not find function "dbConnect"
```


2. Reproduce your above query using the data.table package. Again, try to use a single line of code. [Hint: You first need to convert all db tables to data.table tables].

