24SEP24_tidy_data
================
Stella Koo
2024-09-23

# Functions in `tidyr` package (in `tidyverse`)

``` r
library(tidyverse)
options(tibble.print_min = 5)
```

Use `haven` package to load Pulse Data with SAS file type:

``` r
pulse_df = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") |>
  janitor::clean_names()

pulse_df
```

    ## # A tibble: 1,087 × 7
    ##      id   age sex   bdi_score_bl bdi_score_01m bdi_score_06m bdi_score_12m
    ##   <dbl> <dbl> <chr>        <dbl>         <dbl>         <dbl>         <dbl>
    ## 1 10003  48.0 male             7             1             2             0
    ## 2 10015  72.5 male             6            NA            NA            NA
    ## 3 10022  58.5 male            14             3             8            NA
    ## 4 10026  72.7 male            20             6            18            16
    ## 5 10035  60.4 male             4             0             1             2
    ## # ℹ 1,082 more rows

## `pivot_longer()`

Use `pivot_longer()` function that makes datasets longer by increasing
number of rows and decreasing the number of columns:

``` r
pulse_tidy_df = 
  pivot_longer(
    pulse_df,
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  )

pulse_tidy_df
```

    ## # A tibble: 4,348 × 5
    ##      id   age sex   visit   bdi
    ##   <dbl> <dbl> <chr> <chr> <dbl>
    ## 1 10003  48.0 male  bl        7
    ## 2 10003  48.0 male  01m       1
    ## 3 10003  48.0 male  06m       2
    ## 4 10003  48.0 male  12m       0
    ## 5 10015  72.5 male  bl        6
    ## # ℹ 4,343 more rows

- `names_prefix`: removes common prefix from the column names that are
  pivoting into the “long” format.
- `values_to`: specifies the name of the new column that will store the
  values from the pivoting columns.

The following shows a tidier dataset by changing `bl` to `00m`,
converting `visit` to a factor variable, and rounding `bdi` to integers.

``` r
pulse_df = 
  haven::read_sas("./data_import_examples/public_pulse_data.sas7bdat") |>
  janitor::clean_names() |>
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit", 
    names_prefix = "bdi_score_",
    values_to = "bdi") |>
  mutate(
    visit = replace(visit, visit == "bl", "00m"),
    visit = factor(visit),
    bdi = round(bdi))

print(pulse_df, n = 12)
```

    ## # A tibble: 4,348 × 5
    ##       id   age sex   visit   bdi
    ##    <dbl> <dbl> <chr> <fct> <dbl>
    ##  1 10003  48.0 male  00m       7
    ##  2 10003  48.0 male  01m       1
    ##  3 10003  48.0 male  06m       2
    ##  4 10003  48.0 male  12m       0
    ##  5 10015  72.5 male  00m       6
    ##  6 10015  72.5 male  01m      NA
    ##  7 10015  72.5 male  06m      NA
    ##  8 10015  72.5 male  12m      NA
    ##  9 10022  58.5 male  00m      14
    ## 10 10022  58.5 male  01m       3
    ## 11 10022  58.5 male  06m       8
    ## 12 10022  58.5 male  12m      NA
    ## # ℹ 4,336 more rows

#### Learning Assessment

``` r
litter_tidy_df = 
  read_csv("./data_import_examples/FAS_litters.csv") |>
  janitor::clean_names() |>
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd",
    values_to = "weight") |>
  mutate(gd = recode(gd, gd0_weight = 0, gd18_weight = 18)) |>
  select(litter_number, gd, weight)
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
head(litter_tidy_df)
```

    ## # A tibble: 6 × 3
    ##   litter_number    gd weight
    ##   <chr>         <dbl>  <dbl>
    ## 1 #85               0   19.7
    ## 2 #85              18   34.7
    ## 3 #1/2/95/2         0   27  
    ## 4 #1/2/95/2        18   42  
    ## 5 #5/5/3/83/3-3     0   26  
    ## 6 #5/5/3/83/3-3    18   41.4

## `pivot_wider()`

`pivot_wider()` transforms data from a long to a wide format. Takes
values from a column and spreads them across multiple columns.

``` r
analysis_result = 
  tibble(
    group = c("treatment", "treatment", "placebo", "placebo"),
    time = c("pre", "post", "pre", "post"),
    mean = c(4, 8, 3.5, 4)
  )

analysis_result
```

    ## # A tibble: 4 × 3
    ##   group     time   mean
    ##   <chr>     <chr> <dbl>
    ## 1 treatment pre     4  
    ## 2 treatment post    8  
    ## 3 placebo   pre     3.5
    ## 4 placebo   post    4

``` r
pivot_wider(
  analysis_result, 
  names_from = "time", 
  values_from = "mean")
```

    ## # A tibble: 2 × 3
    ##   group       pre  post
    ##   <chr>     <dbl> <dbl>
    ## 1 treatment   4       8
    ## 2 placebo     3.5     4

## Binding rows

Non-tidiness often stems from relevant data spread across multiple
tables. These tables are basically the same and can be stacked to
produce a tidy dataset.

``` r
fellowship_ring = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "B3:D6") |>
  mutate(movie = "fellowship_ring")

two_towers = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "F3:H6") |>
  mutate(movie = "two_towers")

return_king = 
  readxl::read_excel("./data_import_examples/LotR_Words.xlsx", range = "J3:L6") |>
  mutate(movie = "return_king")
```

``` r
lotr_tidy =
  bind_rows(fellowship_ring, two_towers, return_king) |>
  janitor::clean_names() |>
  pivot_longer(
    female:male,
    names_to = "gender", 
    values_to = "words") |>
  mutate(race = str_to_lower(race)) |> 
  select(movie, everything()) 

lotr_tidy
```

    ## # A tibble: 18 × 4
    ##    movie           race   gender words
    ##    <chr>           <chr>  <chr>  <dbl>
    ##  1 fellowship_ring elf    female  1229
    ##  2 fellowship_ring elf    male     971
    ##  3 fellowship_ring hobbit female    14
    ##  4 fellowship_ring hobbit male    3644
    ##  5 fellowship_ring man    female     0
    ##  6 fellowship_ring man    male    1995
    ##  7 two_towers      elf    female   331
    ##  8 two_towers      elf    male     513
    ##  9 two_towers      hobbit female     0
    ## 10 two_towers      hobbit male    2463
    ## 11 two_towers      man    female   401
    ## 12 two_towers      man    male    3589
    ## 13 return_king     elf    female   183
    ## 14 return_king     elf    male     510
    ## 15 return_king     hobbit female     2
    ## 16 return_king     hobbit male    2673
    ## 17 return_king     man    female   268
    ## 18 return_king     man    male    2459

## Joining Datasets

Data can be spread across multiple related tables, in which case it is
necessary to combine or join them prior to analysis.

- Inner: keeps data that appear in both x and y
- Left: keeps data that appear in x
- Right: keeps data that appear in y
- Full: keeps data that appear in either x or y

``` r
pup_df = 
  read_csv(
    "./data_import_examples/FAS_pups.csv",
    na = c("NA", "", ".")) |>
  janitor::clean_names() |>
  mutate(
    sex = 
      case_match(
        sex, 
        1 ~ "male", 
        2 ~ "female"),
    sex = as.factor(sex)) 

litter_df = 
  read_csv(
    "./data_import_examples/FAS_litters.csv",
    na = c("NA", ".", "")) |>
  janitor::clean_names() |>
  separate(group, into = c("dose", "day_of_tx"), sep = 3) |>
  relocate(litter_number) |>
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    dose = str_to_lower(dose))
```

- `separate(group, into = c("dose", "day_of_tx"), sep = 3)`: splits the
  group column into two new columns: dose and day_of_tx. The split
  occurs after the third character.
- `relocate(litter_number)`: moves the litter_number column to the front
  of the dataframe.

``` r
fas_df = 
  left_join(pup_df, litter_df, by = "litter_number")

fas_df
```

    ## # A tibble: 313 × 15
    ##   litter_number sex   pd_ears pd_eyes pd_pivot pd_walk dose  day_of_tx
    ##   <chr>         <fct>   <dbl>   <dbl>    <dbl>   <dbl> <chr> <chr>    
    ## 1 #85           male        4      13        7      11 con   7        
    ## 2 #85           male        4      13        7      12 con   7        
    ## 3 #1/2/95/2     male        5      13        7       9 con   7        
    ## 4 #1/2/95/2     male        5      13        8      10 con   7        
    ## 5 #5/5/3/83/3-3 male        5      13        8      10 con   7        
    ## # ℹ 308 more rows
    ## # ℹ 7 more variables: gd0_weight <dbl>, gd18_weight <dbl>, gd_of_birth <dbl>,
    ## #   pups_born_alive <dbl>, pups_dead_birth <dbl>, pups_survive <dbl>,
    ## #   wt_gain <dbl>

#### Learning Assessment

- `str_replace_all(id, "student_", "")`: removes “student\_” from the
  rows in `id` column. (from `stringr` package in `tidyverse`)

``` r
surv_od_df = 
  read_csv("./survey_results/surv_os.csv",
    na = c("NA", ".", "")) |>
  janitor::clean_names() |> 
  rename(id = what_is_your_uni, os = what_operating_system_do_you_use) |>
  mutate(id = str_replace_all(id, "student_", ""))
```

    ## Rows: 173 Columns: 2
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): What is your UNI?, What operating system do you use?
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
surv_program_df = read_csv("./survey_results/surv_program_git.csv",
    na = c("NA", ".", "")) |>
  janitor::clean_names() |> 
  rename(
    id = what_is_your_uni, 
    prog = what_is_your_degree_program,
    git_exp = which_most_accurately_describes_your_experience_with_git) |>
    mutate(id = str_replace_all(id, "student_", ""))
```

    ## Rows: 135 Columns: 3
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (3): What is your UNI?, What is your degree program?, Which most accurat...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
surv_df = inner_join(surv_od_df, surv_program_df)
```

    ## Joining with `by = join_by(id)`

    ## Warning in inner_join(surv_od_df, surv_program_df): Detected an unexpected many-to-many relationship between `x` and `y`.
    ## ℹ Row 7 of `x` matches multiple rows in `y`.
    ## ℹ Row 66 of `y` matches multiple rows in `x`.
    ## ℹ If a many-to-many relationship is expected, set `relationship =
    ##   "many-to-many"` to silence this warning.

``` r
print(surv_df, 10)
```

    ## # A
    ## #   tibble:
    ## #   129 ×
    ## #   4
    ##   id   
    ##   <chr>
    ## 1 87   
    ## 2 106  
    ## 3 66   
    ## 4 93   
    ## 5 99   
    ## # ℹ 124
    ## #   more
    ## #   rows
    ## # ℹ 3
    ## #   more
    ## #   variables:
    ## #   os <chr>, …

The `inner_join()` gives warnings about “many-to-many” relationships.
These indicate that the matching variable is not unique. Row 7 of x
matches multiple rows in y:

``` r
surv_od_df |> slice(7)
```

    ## # A tibble: 1 × 2
    ##   id    os        
    ##   <chr> <chr>     
    ## 1 15    Windows 10

``` r
surv_program_df |> filter(id == "15")
```

    ## # A tibble: 2 × 3
    ##   id    prog  git_exp                                                           
    ##   <chr> <chr> <chr>                                                             
    ## 1 15    MPH   Pretty smooth: needed some work to connect Git, GitHub, and RStud…
    ## 2 15    MPH   Pretty smooth: needed some work to connect Git, GitHub, and RStud…

- `slice(7)`: from the `dplyr` package, extracts row 7 from `surv_od_df`
  dataframe.
- `filter(id == "15")`: filters rows with `id == 15`
