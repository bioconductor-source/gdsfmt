gdsfmt: R Interface to CoreArray Genomic Data Structure (GDS) files
===

![LGPLv3](http://www.gnu.org/graphics/lgplv3-88x31.png)
[GNU Lesser General Public License, LGPL-3](https://www.gnu.org/licenses/lgpl.html)

[![Build Status](https://travis-ci.org/zhengxwen/gdsfmt.png)](https://travis-ci.org/zhengxwen/gdsfmt)
[![Build status](https://ci.appveyor.com/api/projects/status/6ussam0n65o32r0j?svg=true)](https://ci.appveyor.com/project/zhengxwen/gdsfmt)
[![codecov.io](https://codecov.io/github/zhengxwen/gdsfmt/coverage.svg?branch=master)](https://codecov.io/github/zhengxwen/gdsfmt?branch=master)


## Features

This package provides a high-level R interface to CoreArray Genomic Data Structure (GDS) data files, which are portable across platforms with hierarchical structure to store multiple scalable array-oriented data sets with metadata information. It is suited for large-scale datasets, especially for data which are much larger than the available random-access memory. The gdsfmt package offers the efficient operations specifically designed for integers of less than 8 bits, since a single genetic/genomic variant, like single-nucleotide polymorphism, usually occupies fewer bits than a byte. Data compression and decompression are also supported with relatively efficient random access. It is allowed to read a GDS file in parallel with multiple R processes supported by the parallel package.


## Bioconductor:

Release Version: v1.6.1

[http://www.bioconductor.org/packages/release/bioc/html/gdsfmt.html](http://www.bioconductor.org/packages/release/bioc/html/gdsfmt.html)

[Help Documents](http://zhengxwen.github.io/gdsfmt/release/help/00Index.html)

Development Version: v1.7.1

[http://www.bioconductor.org/packages/devel/bioc/html/gdsfmt.html](http://www.bioconductor.org/packages/devel/bioc/html/gdsfmt.html)

[Help Documents](http://zhengxwen.github.io/gdsfmt/devel/help/00Index.html)

## Package Vignettes

[http://corearray.sourceforge.net/tutorials/gdsfmt/](http://corearray.sourceforge.net/tutorials/gdsfmt/)

[http://www.bioconductor.org/packages/devel/bioc/vignettes/gdsfmt/inst/doc/gdsfmt_vignette.html](http://www.bioconductor.org/packages/devel/bioc/vignettes/gdsfmt/inst/doc/gdsfmt_vignette.html)


## Citation

Zheng X, Levine D, Shen J, Gogarten SM, Laurie C, Weir BS (2012). A High-performance Computing Toolset for Relatedness and Principal Component Analysis of SNP Data. *Bioinformatics*. [DOI: 10.1093/bioinformatics/bts606](http://dx.doi.org/10.1093/bioinformatics/bts606).


## Package Maintainer

Xiuwen Zheng ([zhengx@u.washington.edu](zhengx@u.washington.edu))


## URL

[http://github.com/zhengxwen/gdsfmt](http://github.com/zhengxwen/gdsfmt)


## Installation

* Bioconductor repository:
```R
source("http://bioconductor.org/biocLite.R")
biocLite("gdsfmt")
```

* Development version from Github:
```R
library("devtools")
install_github("zhengxwen/gdsfmt")
```
The `install_github()` approach requires that you build from source, i.e. `make` and compilers must be installed on your system -- see the [R FAQ](http://cran.r-project.org/faqs.html) for your operating system; you may also need to install dependencies manually.



## Copyright Notice

* CoreArray C++ library, LGPL-3 License, 2007-2015, Xiuwen Zheng
* zlib, zlib License, 1995-2015, Jean-loup Gailly and Mark Adler
* LZ4, BSD 2-clause License, 2011-2015, Yann Collet
* [README](./inst/COPYRIGHTS)



## GDS Command-line Tools

### viewgds

`viewgds` is a shell script written in R ([viewgds.R](https://github.com/zhengxwen/Documents/blob/master/Program/viewgds.R)), to view the contents of a GDS file. The R packages `gdsfmt`, `getopt` and `optparse` should be installed before running `viewgds`, and the package `crayon` is optional.

In the R environment,
```R
install.packages("getopt", repos="http://cran.r-project.org")
install.packages("optparse", repos="http://cran.r-project.org")
install.packages("crayon", repos="http://cran.r-project.org")

source("http://bioconductor.org/biocLite.R")
biocLite("gdsfmt")
```

Installation with command line,
```sh
echo '#!' `which Rscript` '--vanilla' > viewgds
curl -L https://raw.githubusercontent.com/zhengxwen/Documents/master/Program/viewgds.R >> viewgds
chmod +x viewgds

## Or
echo '#!' `which Rscript` '--vanilla' > viewgds
wget -qO- --no-check-certificate https://raw.githubusercontent.com/zhengxwen/Documents/master/Program/viewgds.R >> viewgds
chmod +x viewgds
```

[See More...](https://github.com/zhengxwen/Documents/tree/master/Program)


## Examples

```R
library(gdsfmt)

# create a GDS file
f <- createfn.gds("test.gds")

add.gdsn(f, "int", val=1:10000)
add.gdsn(f, "double", val=seq(1, 1000, 0.4))
add.gdsn(f, "character", val=c("int", "double", "logical", "factor"))
add.gdsn(f, "logical", val=rep(c(TRUE, FALSE, NA), 50))
add.gdsn(f, "factor", val=as.factor(c(NA, "AA", "CC")))
add.gdsn(f, "bit2", val=sample(0:3, 1000, replace=TRUE), storage="bit2")

# list and data.frame
add.gdsn(f, "list", val=list(X=1:10, Y=seq(1, 10, 0.25)))
add.gdsn(f, "data.frame", val=data.frame(X=1:19, Y=seq(1, 10, 0.5)))

folder <- addfolder.gdsn(f, "folder")
add.gdsn(folder, "int", val=1:1000)
add.gdsn(folder, "double", val=seq(1, 100, 0.4))

# show the contents
f

# close the GDS file
closefn.gds(f)
```

```
File: test.gds (1.1 KB)
+    [  ]
|--+ int   { Int32 10000, 40.0 KB }
|--+ double   { Float64 2498, 20.0 KB }
|--+ character   { VStr8 4, 26 bytes }
|--+ logical   { Int32,logical 150, 600 bytes } *
|--+ factor   { Int32,factor 3, 12 bytes } *
|--+ bit2   { Bit2 1000, 250 bytes }
|--+ list   [ list ] *
|  |--+ X   { Int32 10, 40 bytes }
|  |--+ Y   { Float64 37, 296 bytes }
|--+ data.frame   [ data.frame ] *
|  |--+ X   { Int32 19, 76 bytes }
|  |--+ Y   { Float64 19, 152 bytes }
|--+ folder   [  ]
|  |--+ int   { Int32 1000, 4.0 KB }
|  |--+ double   { Float64 248, 2.0 KB }
```
