
```table-of-contents
```

---
## Environment

A common setup is to use **rig** for **R versions** and **renv** for **project packages**.
### R version - rig
The most popular tool for this is **rig** (the R Installation Manager), developed by Gábor Csárdi as part of the r-lib project at Posit.

[Getting started – rig](https://rig.r-lib.org/tutorial.html)


```bash
rig add release      # install the latest R release
rig add 4.3          # install a specific version
rig list             # show installed versions
rig default 4.4      # set the default version
rig rm 4.2           # remove a version
rig rstudio 4.3      # open RStudio with a specific R version
```

Installation: Details see [Installing rig – rig](https://rig.r-lib.org/install.html)
- macOS: Homebrew
- Windows: go to the website and download installers)

```bash
# Method 1: Install the lastest R version inside your Windows user account
rig --user add release

# Method 2: Open PowerShell with un as administrator
# makes R available to all Windows users
rig --admin add release
rig --admin list
```

For Method 1, we can set user as the default mode:
```bash
rig config set mode=user
rig add release
# Then open a new PowerShell window and verify:
rig list
R --version
# This installs a separate R release under your user profile; 
#it does not delete or alter existing R versions. 
# You can still inspect the old system installations later with:
rig --admin list
```


### renv Environment

```r
install.packages("renv")
renv::init()
renv::install()
renv::snapshot()
```

### rv package (A modern method)
Alternatively, there is a newer package called rv, which seems to record the R version and package versions. It looks like something similar to `uv` for Python. (Never try it but it worths testing)

[https://a2-ai.github.io/rv-docs/](https://a2-ai.github.io/rv-docs/ "https://a2-ai.github.io/rv-docs/")

`rv + rig` is a more modern R workflow to handle R packages and R versions.
https://antoinelucasfra.github.io/posts/rv-modern-r-package-manager/

### Styling
Automatic reformatter (modern): `air`
Manual reformatter: `styler`
Lint check: [lintr](https://lintr.r-lib.org/)



## Data

### Basic
```r
# Show the first 5 rows of your dataframe
head(df)

# Show the first 10 rows of your dataframe
head(df, n=10)
```

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


## Plot

```r
library(ggplot2)
library(tidyr)

df <- data.frame(
  name = c("A", "A", "A", "B", "B", "B"),
  value_1 = 1:6,
  value_2 = 101:106
)

# 1. Reshape the data from wide to long
df_long <- pivot_longer(df, 
                        cols = c(value_1, value_2), 
                        names_to = "variable", 
                        values_to = "value")

# 2. Convert to factor and set the exact order you want
df_long$variable <- factor(df_long$variable, levels = c("value_2", "value_1"))

# 3. Plot using Method 2 and facet_wrap
ggplot(df_long, aes(x = name, y = value)) +
  stat_summary(fun = mean, geom = "bar", fill = "steelblue", color = "black") +
  stat_summary(fun.data = mean_se, geom = "errorbar", width = 0.2) +
  # Splits into two side-by-side plots
  facet_wrap(~ variable, scales = "free_y") +  
  # Add ncol = 1 to stack vertically
  # facet_wrap(~ variable, ncol = 1, scales = "free_y") +
  coord_cartesian(ylim = c(0, 20)) +
  labs(title = "Mean Values with Standard Error",
       x = "Name",
       y = "Mean")+
  theme(axis.text.x = element_text(size = 6))
```

## Functions
```r
function_name <- function(arg1, arg2) {
  # Code to execute goes here
  result <- arg1 + arg2
  return(result) 
}
```

## Package Development (devtools)

```r
library(devtools)
document()
load_all()
check()
test()
```

Use [`usethis`]([Automate Package and Project Setup • usethis](https://usethis.r-lib.org/)) package for R package development

```r
install.packages(c("usethis", "devtools"))

# Create a new package
library(usethis)
usethis::create_package("new_package_name")

# create a new .R file below R/
use_r("new_function_name")

# Add some useful packages
use_package("rmarkdown", "Suggests")
```

Use roxygen2 [In-Line Documentation for R • roxygen2](https://roxygen2.r-lib.org/index.html)  to help documente packages in a standard way

Insert the Roxygen skeleton in Positron
1. Place your cursor on the function name
2. Look for the lightbulb icon (💡) beside the code.
3. Click the lightbulb.
4. Select the action to generate a Roxygen documentation skeleton.
5. Then run `devtools::document()`