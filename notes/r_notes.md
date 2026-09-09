# R Notes

Environment management, data cleaning, and package development in R.

_Merged from `common_r_codes.txt` and `r_package_notes.txt`._

## Table of Contents

- [renv Environment](#renv-environment)
- [Data Cleaning (janitor)](#data-cleaning-janitor)
- [dplyr Verbs](#dplyr-verbs)
- [Package Development (devtools)](#package-development-devtools)

---

## renv Environment

```r
install.packages("renv")
renv::init()
renv::install()
renv::snapshot()
```

## Data Cleaning (janitor)

```r
# Cleanup column names
janitor::clean_names()
```

## dplyr Verbs

```r
mutate()
rename()
arrange()
filter()
select()
```

## Package Development (devtools)

```r
library(devtools)
document()
load_all()
check()
test()
```
