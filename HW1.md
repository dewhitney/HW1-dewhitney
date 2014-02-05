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
## Warning: downloaded length 228765 != reported length 235649
## Warning: error 1 in extracting from zip file
## Warning: cannot open compressed file 'GEOmetadb/DESCRIPTION', probable reason 'No such file or directory'
```

```
## Error: cannot open the connection
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


Now to get the 

```r
# This database download is particularly slow, so eval=FALSE for this code
# chunk
getSQLiteFile()
```



```r
# Make sure your current working directory contains GEOmetadb.sqlite
geo_con = dbConnect(SQLite(), "GEOmetadb.sqlite")
```



```r
# Join gse and gpl via the gse_gpl table
res = dbGetQuery(geo_con, "SELECT gse.title, gse.gse, gpl.gpl, gpl.manufacturer, gpl.technology FROM (gse JOIN gse_gpl ON gse.gse = gse_gpl.gse) j JOIN gpl ON j.gpl=gpl.gpl WHERE gpl.manufacturer LIKE '%Illumina%' AND gse.contact LIKE '%Yale%' AND (gse.title LIKE '%HCV%' OR gse.title LIKE '%Hepatitis C%') ")
res
```

```
##                                                                                                          gse.title
## 1                                               The blood transcriptional signature of chronic HCV [Illumina data]
## 2                                                               The blood transcriptional signature of chronic HCV
## 3 Impaired TLR3-mediated immune responses from macrophages of patients chronically infected with Hepatitis C virus
##    gse.gse  gpl.gpl gpl.manufacturer        gpl.technology
## 1 GSE40223 GPL10558    Illumina Inc. oligonucleotide beads
## 2 GSE40224 GPL10558    Illumina Inc. oligonucleotide beads
## 3 GSE40812 GPL10558    Illumina Inc. oligonucleotide beads
```


2. Reproduce your above query using the data.table package. Again, try to use a single line of code. [Hint: You first need to convert all db tables to data.table tables].

**Answer**


```r
# Working directory must contain the GEOmetadb.sqlite
library(data.table)

# Store each table for our query from the db in data.tables
gse.dt = data.table(dbGetQuery(geo_con, "SELECT gse.title, gse.gse, gse.contact FROM gse"))
gpl.dt = data.table(dbGetQuery(geo_con, "SELECT gpl.gpl, gpl.manufacturer, gpl.technology FROM gpl"))
gse_gpl.dt = data.table(dbGetQuery(geo_con, "SELECT gse_gpl.gse, gse_gpl.gpl FROM gse_gpl"))

# Set keys and then join the tables on gse
setkey(gse.dt, gse)
setkey(gse_gpl.dt, gse)
gse_gse_gpl.dt = gse.dt[gse_gpl.dt]

# Set keys and then join the new table and gpl table on gpl
setkey(gpl.dt, gpl)
setkey(gse_gse_gpl.dt, gpl)
merge.gse.gpl.dt = merge(gse_gse_gpl.dt, gpl.dt, all = TRUE)

# The final query for title, accession numbers, manufacturer, platform
merge.gse.gpl.dt[(title %like% "HCV" | title %like% "Hepatitis C") & manufacturer %like% 
    "Illumina" & contact %like% "Yale"]
```

```
##         gpl      gse
## 1: GPL10558 GSE40223
## 2: GPL10558 GSE40224
## 3: GPL10558 GSE40812
##                                                                                                               title
## 1:                                               The blood transcriptional signature of chronic HCV [Illumina data]
## 2:                                                               The blood transcriptional signature of chronic HCV
## 3: Impaired TLR3-mediated immune responses from macrophages of patients chronically infected with Hepatitis C virus
##                                                                                                                                                                                                                                                  contact
## 1: Name: Christopher Bolen;\tEmail: christopher.bolen@yale.edu;\tLaboratory: Steven Kleinstein;\tDepartment: Pathology;\tInstitute: Yale University;\tAddress: 300 George Street, Suite 505;\tCity: New Haven;\tState: CT;\tZip/postal_code: 06511;\tCountry: USA
## 2: Name: Christopher Bolen;\tEmail: christopher.bolen@yale.edu;\tLaboratory: Steven Kleinstein;\tDepartment: Pathology;\tInstitute: Yale University;\tAddress: 300 George Street, Suite 505;\tCity: New Haven;\tState: CT;\tZip/postal_code: 06511;\tCountry: USA
## 3:                      Name: Ruth,R,Montgomery;\tEmail: ruth.montgomery@yale.edu;\tDepartment: Internal Medicine;\tInstitute: Yale University School of Medicine;\tAddress: 300 Cedar St.;\tCity: New Haven;\tState: CT;\tZip/postal_code: 06520;\tCountry: USA
##     manufacturer            technology
## 1: Illumina Inc. oligonucleotide beads
## 2: Illumina Inc. oligonucleotide beads
## 3: Illumina Inc. oligonucleotide beads
```

