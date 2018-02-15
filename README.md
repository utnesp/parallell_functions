Repository containing functions to do parallell computations


```R
source_https <- function(url, ...) {
  # load package
  require(RCurl)
 
  # parse and evaluate each .R script
  sapply(c(url, ...), function(u) {
    eval(parse(text = getURL(u, followlocation = TRUE, cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl"))), envir = .GlobalEnv)
  })
}
```

```R
# You will need these packages
library(survival)
library(data.table)
library(ggplot2) # only if plotting data afterwards
library(survminer) # only if plotting data afterwards
library(foreach)
library(doParallel)

source_https("https://raw.githubusercontent.com/utnesp/parallell_functions/master/R/coxph.parallell")
```

The coxph.parallell function is as follows:

coxph.parallell <- function(df, time.event.data, var.list = NULL, file = "test.txt", no_cores = "")

input df is a data.table with gene in rows and samples in col. first col has to contain name of gene. 
var.list is extracted from first column
```R
> head(df)
var     s1  s2  s3
gene_1  1   0   1
gene_2  1   0   1
gene_3  1   1   1
```
The df can also contain continous values

time.event.data is data.frame / data.table with cols binary data (event) in col1 and time in col2. The row order has to match col order in df
```R
> head(time.event.data)
    event   time
s1  1       1302
s2  0       1200
s3  1       1400
```

The var.list is set to NULL by default, meaning that the first column in df will be used as variables. The function writes to a number of temporary files, and when done, 
concatenates the files to the file specified in file argument. The function will use all.cores - 1 if not specified.

Plot data using the plot.surv function
```R
plot.surv(genename, time.event.data, df)
```

If you also know which sample are e.g. highrisk, then you can subset your data in the above two functions
```R
> head(clinicalData)
    highrisk
s1  1       
s2  0       
s3  1       

coxph.parallell(df, time.event.data, sub.idx = which(SEQC.clinicalData$highrisk == 1), file = "res.txt")
res <- fread("res.txt")

plot.surv(genename, time.event.data, df, sub.idx = which(SEQC.clinicalData$highrisk == 1))
```



