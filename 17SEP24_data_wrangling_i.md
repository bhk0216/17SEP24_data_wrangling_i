17SEP24_data_wrangling_i
================
Stella Koo
2024-09-15

### Two kinds of paths:

- Absolute: a file or folder’s “full address” on the computer
  - If directory moved, or project shared, absolute path to files will
    change and code won’t work
- Relative: directions to a file or folder from the current working
  directory
  - Portable, if directory moved or shared, the relative path will stay
    the same and code will work

### Shorthand Notation for Pathnames

- `~` Home Directory
- `.` Current working directory
- `..` One directory up from current working directory
- `../..` Two directories up from current working directory
- `gitignore` file: specifies intentionally untracked files that Git
  should ignore.

### Importing data tables

Use function from `readr` (included in tidyverse) to import data.

- `janitor::clean_names()`: standardizes column names by converting them
  to a consistent, machine-friendly format
- `package::function` allows using a function from a package without
  loading the whole library (e.g. `dplyr::filter`)
- `conflicted` package: helps resolve ambiguity by forcing to explicitly
  choose which function to use when there’s a conflict

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.1
    ## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.1
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
litters_df = read_csv(file = "./data_import_examples/FAS_litters.csv") # relative path
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
names(litters_df) 
```

    ## [1] "Group"             "Litter Number"     "GD0 weight"       
    ## [4] "GD18 weight"       "GD of Birth"       "Pups born alive"  
    ## [7] "Pups dead @ birth" "Pups survive"

``` r
# e.g Litter Number

litters_df = janitor::clean_names(litters_df)
names(litters_df)
```

    ## [1] "group"           "litter_number"   "gd0_weight"      "gd18_weight"    
    ## [5] "gd_of_birth"     "pups_born_alive" "pups_dead_birth" "pups_survive"

``` r
# with janitor::clean_names() function -> litter_number
```

``` r
pups_df = read_csv("~/Desktop/Columbia Biostatistics MS/Courses/P8105/Lecture Notes/17SEP24_data_wrangling_i/data_import_examples/FAS_pups.csv") # absolute path
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

### Looking at data

- `view`: opens a spreadsheet-like view of data frame (does not work
  well inside RMD files)
- `str`: displays structure of R object (e.g data types of each column
  for data frames)
- `head`:returns the first few rows of data frame
- `tail`: returns the last few rows of data frame
- `skimr::skim()` function: part of the skimr package, which provides a
  more comprehensive summary of data frames

``` r
tail(litters_df, 5)
```

    ## # A tibble: 5 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Low8  #100                20          39.2          20               8
    ## 2 Low8  #4/84               21.8        35.2          20               4
    ## 3 Low8  #108                25.6        47.5          20               8
    ## 4 Low8  #99                 23.5        39            20               6
    ## 5 Low8  #110                25.5        42.7          20               7
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

``` r
skimr::skim(litters_df)
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | litters_df |
| Number of rows                                   | 49         |
| Number of columns                                | 8          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 2          |
| numeric                                          | 6          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim_variable | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:--------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| group         |         0 |             1 |   4 |   4 |     0 |        6 |          0 |
| litter_number |         0 |             1 |   3 |  15 |     0 |       49 |          0 |

**Variable type: numeric**

| skim_variable   | n_missing | complete_rate |  mean |   sd |   p0 |   p25 |   p50 |   p75 | p100 | hist  |
|:----------------|----------:|--------------:|------:|-----:|-----:|------:|------:|------:|-----:|:------|
| gd0_weight      |        15 |          0.69 | 24.38 | 3.28 | 17.0 | 22.30 | 24.10 | 26.67 | 33.4 | ▃▇▇▆▁ |
| gd18_weight     |        17 |          0.65 | 41.52 | 4.05 | 33.4 | 38.88 | 42.25 | 43.80 | 52.7 | ▃▃▇▂▁ |
| gd_of_birth     |         0 |          1.00 | 19.65 | 0.48 | 19.0 | 19.00 | 20.00 | 20.00 | 20.0 | ▅▁▁▁▇ |
| pups_born_alive |         0 |          1.00 |  7.35 | 1.76 |  3.0 |  6.00 |  8.00 |  8.00 | 11.0 | ▁▃▂▇▁ |
| pups_dead_birth |         0 |          1.00 |  0.33 | 0.75 |  0.0 |  0.00 |  0.00 |  0.00 |  4.0 | ▇▂▁▁▁ |
| pups_survive    |         0 |          1.00 |  6.41 | 2.05 |  1.0 |  5.00 |  7.00 |  8.00 |  9.0 | ▁▃▂▇▇ |

### Arguments to `read*`

- `col_names`: default is TRUE (first row of the dataset is
  automatically used as the column names). FALSE generic column names
  like `X1`, `X2`, …, are assigned
- `na`: specify which values in data should be treated as missing
  values. (e.g. na = “N/A”)
- `skip`: skips a specified number of rows before reading the data

Run `? read_csv()` to see what other arguments there are in addition to
these.

``` r
litters_df_2 = 
    read_csv(file = "./data_import_examples/FAS_litters.csv",
    skip = 10, col_names = FALSE)
```

    ## Rows: 40 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): X1, X2
    ## dbl (6): X3, X4, X5, X6, X7, X8
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
head(litters_df_2)
```

    ## # A tibble: 6 × 8
    ##   X1    X2                 X3    X4    X5    X6    X7    X8
    ##   <chr> <chr>           <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
    ## 1 Con8  #3/5/2/2/95      28.5    NA    20     8     0     8
    ## 2 Con8  #5/4/3/83/3      28      NA    19     9     0     8
    ## 3 Con8  #1/6/2/2/95-2    NA      NA    20     7     0     6
    ## 4 Con8  #3/5/3/83/3-3-2  NA      NA    20     8     0     8
    ## 5 Con8  #2/2/95/2        NA      NA    19     5     0     4
    ## 6 Con8  #3/6/2/2/95-3    NA      NA    20     7     0     7

``` r
litters_df_3 = 
    read_csv(
        file = "./data_import_examples/FAS_litters.csv",
        na = c(".", "NA", ""))
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
head(litters_df_3)
```

    ## # A tibble: 6 × 8
    ##   Group `Litter Number` `GD0 weight` `GD18 weight` `GD of Birth`
    ##   <chr> <chr>                  <dbl>         <dbl>         <dbl>
    ## 1 Con7  #85                     19.7          34.7            20
    ## 2 Con7  #1/2/95/2               27            42              19
    ## 3 Con7  #5/5/3/83/3-3           26            41.4            19
    ## 4 Con7  #5/4/2/95/2             28.5          44.1            19
    ## 5 Con7  #4/2/95/3-3             NA            NA              20
    ## 6 Con7  #2/2/95/3-2             NA            NA              20
    ## # ℹ 3 more variables: `Pups born alive` <dbl>, `Pups dead @ birth` <dbl>,
    ## #   `Pups survive` <dbl>

### Parsing columns

`read_* functions` (e.g. `read_csv`) will automatically guess the data
type of columns. However, if you want to give explicit column
specifications use `cols` functions:

``` r
litters_df_4= 
    read_csv(file = "./data_import_examples/FAS_litters.csv",
        na = c(".", "NA", ""),
    col_types = cols(
      Group = col_factor(),
      `Litter Number` = col_character(),
      `GD0 weight` = col_double(),
      `GD18 weight` = col_double(),
      `GD of Birth` = col_integer(),
      `Pups born alive` = col_integer(),
      `Pups dead @ birth` = col_integer(),
      `Pups survive` = col_integer()
    )
  )

tail(litters_df_4)
```

    ## # A tibble: 6 × 8
    ##   Group `Litter Number` `GD0 weight` `GD18 weight` `GD of Birth`
    ##   <fct> <chr>                  <dbl>         <dbl>         <int>
    ## 1 Low8  #79                     25.4          43.8            19
    ## 2 Low8  #100                    20            39.2            20
    ## 3 Low8  #4/84                   21.8          35.2            20
    ## 4 Low8  #108                    25.6          47.5            20
    ## 5 Low8  #99                     23.5          39              20
    ## 6 Low8  #110                    25.5          42.7            20
    ## # ℹ 3 more variables: `Pups born alive` <int>, `Pups dead @ birth` <int>,
    ## #   `Pups survive` <int>

``` r
pups_df_2 = read_csv(file = "./data_import_examples/FAS_pups.csv",
                     na = c(".", "NA"),
                     col_types = "fddddd" # factor, double, double, ...
)

head(pups_df_2)
```

    ## # A tibble: 6 × 6
    ##   `Litter Number`   Sex `PD ears` `PD eyes` `PD pivot` `PD walk`
    ##   <fct>           <dbl>     <dbl>     <dbl>      <dbl>     <dbl>
    ## 1 #85                 1         4        13          7        11
    ## 2 #85                 1         4        13          7        12
    ## 3 #1/2/95/2           1         5        13          7         9
    ## 4 #1/2/95/2           1         5        13          8        10
    ## 5 #5/5/3/83/3-3       1         5        13          8        10
    ## 6 #5/5/3/83/3-3       1         5        14          6         9

### Other file formats

For Excel files, use `readxl` package that has `read_excel` function.

- `sheet`option: useful when there are multiple sheets in the Excel file
- `range` option: want to read in a specific data rectangle

``` r
library(readxl)

mlb11_df = read_excel("./data_import_examples/mlb11.xlsx", n_max = 20)

head(mlb11_df, 5)
```

    ## # A tibble: 5 × 12
    ##   team         runs at_bats  hits homeruns bat_avg strikeouts stolen_bases  wins
    ##   <chr>       <dbl>   <dbl> <dbl>    <dbl>   <dbl>      <dbl>        <dbl> <dbl>
    ## 1 Texas Rang…   855    5659  1599      210   0.283        930          143    96
    ## 2 Boston Red…   875    5710  1600      203   0.28        1108          102    90
    ## 3 Detroit Ti…   787    5563  1540      169   0.277       1143           49    95
    ## 4 Kansas Cit…   730    5672  1560      129   0.275       1006          153    71
    ## 5 St. Louis …   762    5532  1513      162   0.273        978           57    90
    ## # ℹ 3 more variables: new_onbase <dbl>, new_slug <dbl>, new_obs <dbl>

For SAS, Stata, SPSS files, use `haven` package.

``` r
library(haven)

pulse_df = read_sas("./data_import_examples/public_pulse_data.sas7bdat")

head(pulse_df, 5)
```

    ## # A tibble: 5 × 7
    ##      ID   age Sex   BDIScore_BL BDIScore_01m BDIScore_06m BDIScore_12m
    ##   <dbl> <dbl> <chr>       <dbl>        <dbl>        <dbl>        <dbl>
    ## 1 10003  48.0 male            7            1            2            0
    ## 2 10015  72.5 male            6           NA           NA           NA
    ## 3 10022  58.5 male           14            3            8           NA
    ## 4 10026  72.7 male           20            6           18           16
    ## 5 10035  60.4 male            4            0            1            2

### Comparisons with Base R

`read.csv`, `read.table`, etc are base R functions (different from
`read_csv` that come from `readr` package, outputs a tibble, and does
NOT convert character cols to factors). \* tend to be slower \* outputs
a data frame \* automatically convert characters to factors (less
control over data types)

``` r
pups_base = read.csv("./data_import_examples/FAS_pups.csv")
pups_readr = read_csv("./data_import_examples/FAS_pups.csv")
```

    ## Rows: 313 Columns: 6
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
head(pups_base, 5)
```

    ##   Litter.Number Sex PD.ears PD.eyes PD.pivot PD.walk
    ## 1           #85   1       4      13        7      11
    ## 2           #85   1       4      13        7      12
    ## 3     #1/2/95/2   1       5      13        7       9
    ## 4     #1/2/95/2   1       5      13        8      10
    ## 5 #5/5/3/83/3-3   1       5      13        8      10

``` r
head(pups_readr, 5)
```

    ## # A tibble: 5 × 6
    ##   `Litter Number`   Sex `PD ears` `PD eyes` `PD pivot` `PD walk`
    ##   <chr>           <dbl>     <dbl>     <dbl>      <dbl>     <dbl>
    ## 1 #85                 1         4        13          7        11
    ## 2 #85                 1         4        13          7        12
    ## 3 #1/2/95/2           1         5        13          7         9
    ## 4 #1/2/95/2           1         5        13          8        10
    ## 5 #5/5/3/83/3-3       1         5        13          8        10

``` r
pups_base$S
```

    ##   [1] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2
    ##  [38] 2 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2
    ##  [75] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1
    ## [112] 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1
    ## [149] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2
    ## [186] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1 1 1 1 1 1 1
    ## [223] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 1
    ## [260] 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 2 2 2 2 2 2 2 2 2 2 2 2 2
    ## [297] 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2

``` r
pups_readr$S
```

    ## Warning: Unknown or uninitialised column: `S`.

    ## NULL

## Exporting data

Use `write_*` function in `readr` package. (e.g. write_csv(data_frame,
“output.csv”))
