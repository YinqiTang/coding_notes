
## Table of Contents
```table-of-contents
```

---
## Environment

A common setup is to use **rig** for **R versions** and **renv** for **project packages**.
### R version
The most popular tool for this is **rig** (the R Installation Manager), developed by Gábor Csárdi as part of the r-lib project at Posit.

```bash
rig add release      # install the latest R release
rig add 4.3          # install a specific version
rig list             # show installed versions
rig default 4.4      # set the default version
rig rm 4.2           # remove a version
rig rstudio 4.3      # open RStudio with a specific R version
```

Installation:
- macOS: Homebrew (`brew tap r-lib/rig && brew install --cask rig`), 
- Windows: winget (`winget install posit.rig`) (or go to the website and download installers)
- Linux instructions and downloads are on the GitHub repo at github.com/r-lib/rig.

### renv Environment

```r
install.packages("renv")
renv::init()
renv::install()
renv::snapshot()
```

### rv package
Alternatively, there is a newer package called rv, which seems to record the R version and package versions. It looks like something similar to `uv` for Python. (Never try it but it worths testing)

[https://a2-ai.github.io/rv-docs/](https://a2-ai.github.io/rv-docs/ "https://a2-ai.github.io/rv-docs/")

`rv + rig` is a more modern R workflow to handle R packages and R versions.
https://antoinelucasfra.github.io/posts/rv-modern-r-package-manager/

## Data



### Data cleaning
```r
# Cleanup column names
janitor::clean_names()
```

### `dplyr` package

```r
mutate()
rename()
arrange()
filter()
select()
case_when()
```

## Package Development (devtools)

```r
library(devtools)
document()
load_all()
check()
test()
```
