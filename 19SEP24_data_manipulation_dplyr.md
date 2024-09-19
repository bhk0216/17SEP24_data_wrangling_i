19SEP24_data_manipulation_dplyr
================
Stella Koo
2024-09-18

# Functions in `dplyr` package (in `tidyverse`)

`options()` sets global defaults for the session in which that specific
RMD is executed. They will apply to the entire document unless
overridden later in the code. ONLY affects console. Output file for
HTML, PDF, etc (except github) shows many rows.

``` r
library(tidyverse)

options(tibble.print_min = 3)

litters_df = read_csv("./data_import_examples/FAS_litters.csv", na = c("NA", ".", ""))
litters_df = janitor::clean_names(litters_df)

pups_df = read_csv("./data_import_examples/FAS_pups.csv", na = c("NA", "."))
pups_df = janitor::clean_names(pups_df)
```

## `Select`

`select` allows to specify a subset of columns in a data table.

- Can specify the columns you want to keep by listing all of them.

``` r
select(litters_df, group, litter_number, gd0_weight, pups_born_alive)
```

    ## # A tibble: 49 × 4
    ##   group litter_number gd0_weight pups_born_alive
    ##   <chr> <chr>              <dbl>           <dbl>
    ## 1 Con7  #85                 19.7               3
    ## 2 Con7  #1/2/95/2           27                 8
    ## 3 Con7  #5/5/3/83/3-3       26                 6
    ## # ℹ 46 more rows

- Can specify a range of columns to keep.

``` r
select(litters_df, group:gd_of_birth)
```

    ## # A tibble: 49 × 5
    ##   group litter_number gd0_weight gd18_weight gd_of_birth
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>
    ## 1 Con7  #85                 19.7        34.7          20
    ## 2 Con7  #1/2/95/2           27          42            19
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19
    ## # ℹ 46 more rows

- Can specify which columns to remove.

``` r
select(litters_df, -pups_survive)
```

    ## # A tibble: 49 × 7
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 1 more variable: pups_dead_birth <dbl>

- Can rename variables as part of the process.

``` r
select(litters_df, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 2
    ##   GROUP LiTtEr_NuMbEr
    ##   <chr> <chr>        
    ## 1 Con7  #85          
    ## 2 Con7  #1/2/95/2    
    ## 3 Con7  #5/5/3/83/3-3
    ## # ℹ 46 more rows

- If want to rename columns while keeping everything, use `rename`
  instead of `select`.

``` r
rename(litters_df, GROUP = group, LiTtEr_NuMbEr = litter_number)
```

    ## # A tibble: 49 × 8
    ##   GROUP LiTtEr_NuMbEr gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #1/2/95/2           27          42            19               8
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

- Use helper functions, list available in `?select_helpers`. E.g.
  `start_with()`, `ends_with()`, `contains()`)

``` r
select(litters_df, starts_with("gd"))
```

    ## # A tibble: 49 × 3
    ##   gd0_weight gd18_weight gd_of_birth
    ##        <dbl>       <dbl>       <dbl>
    ## 1       19.7        34.7          20
    ## 2       27          42            19
    ## 3       26          41.4          19
    ## # ℹ 46 more rows

- Use `everything()` helper function to reorganize columns without
  discarding anything.

``` r
select(litters_df, litter_number, pups_survive, everything())
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_born_alive <dbl>, pups_dead_birth <dbl>

- `relocate` function does the same thing as `everything` helper
  function used inside `select`.

``` r
relocate(litters_df, litter_number, pups_survive)
```

    ## # A tibble: 49 × 8
    ##   litter_number pups_survive group gd0_weight gd18_weight gd_of_birth
    ##   <chr>                <dbl> <chr>      <dbl>       <dbl>       <dbl>
    ## 1 #85                      3 Con7        19.7        34.7          20
    ## 2 #1/2/95/2                7 Con7        27          42            19
    ## 3 #5/5/3/83/3-3            5 Con7        26          41.4          19
    ## # ℹ 46 more rows
    ## # ℹ 2 more variables: pups_born_alive <dbl>, pups_dead_birth <dbl>

`select` will export a data frame even if only one column is selected.
Use `pull` if only vector of that column wanted.

#### Learning Assessment

``` r
select(pups_df, litter_number:pd_ears)
```

    ## # A tibble: 313 × 3
    ##   litter_number   sex pd_ears
    ##   <chr>         <dbl>   <dbl>
    ## 1 #85               1       4
    ## 2 #85               1       4
    ## 3 #1/2/95/2         1       5
    ## # ℹ 310 more rows

## `Filter`

- Uses comparison operators (`>`, `>=`, `<`, `<=`, `==`, and `!=`)
- `%in%` to detect if values appear in a set
- `is.na()` to find missing values
- can be combined with other comparisons using the logical operators `&`
  and `|`, or negated using `!`
- Examples:

gd_of_birth == 20 pups_born_alive \>= 2 pups_survive != 4 !(pups_survive
== 4) group %in% c(“Con7”, “Con8”) group == “Con7” & gd_of_birth == 20

``` r
filter_litters_df = litters_df %>% 
  select(group, pups_survive) %>% 
  filter(group %in% c("Con7", "Con8"), pups_survive != 4)

filter_litters_df
```

    ## # A tibble: 12 × 2
    ##    group pups_survive
    ##    <chr>        <dbl>
    ##  1 Con7             3
    ##  2 Con7             7
    ##  3 Con7             5
    ##  4 Con7             6
    ##  5 Con7             9
    ##  6 Con8             8
    ##  7 Con8             8
    ##  8 Con8             8
    ##  9 Con8             8
    ## 10 Con8             6
    ## 11 Con8             8
    ## 12 Con8             7

To omit missing observations, it is better to use `drop_na` from `tidyr`
package:

- `drop_na(litters_df)` will remove any row with a missing value.
- `drop_na(litters_df, wt_increase)` will remove rows for which
  `wt_increase` is missing.

#### Learning Assessment

``` r
filter(pups_df, sex == 2, pd_walk < 11)
```

    ## # A tibble: 127 × 6
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>
    ## 1 #1/2/95/2         2       4      13        7       9
    ## 2 #1/2/95/2         2       4      13        7      10
    ## 3 #1/2/95/2         2       5      13        8      10
    ## # ℹ 124 more rows

## `Mutate`

`mutate()` is used add new columns or change existing ones based on
calculations or transformations.

``` r
mutate(litters_df,
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group)
)
```

    ## # A tibble: 49 × 9
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 46 more rows
    ## # ℹ 3 more variables: pups_dead_birth <dbl>, pups_survive <dbl>, wt_gain <dbl>

#### Learning Assessment

``` r
mutate(pups_df, 
       pd_pivot_minus7 = pd_pivot - 7,
       sum_pd = pd_ears + pd_eyes + pd_pivot + pd_walk
       )
```

    ## # A tibble: 313 × 8
    ##   litter_number   sex pd_ears pd_eyes pd_pivot pd_walk pd_pivot_minus7 sum_pd
    ##   <chr>         <dbl>   <dbl>   <dbl>    <dbl>   <dbl>           <dbl>  <dbl>
    ## 1 #85               1       4      13        7      11               0     35
    ## 2 #85               1       4      13        7      12               0     36
    ## 3 #1/2/95/2         1       5      13        7       9               0     34
    ## # ℹ 310 more rows

## `Arrange`

`arrange()` reorders rows based on the values of one or more columns. By
deafult, arranges in ascending order. Use `desc()` helper function for
descending order.

``` r
head(arrange(litters_df, group, pups_born_alive), 6)
```

    ## # A tibble: 6 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 Con7  #85                 19.7        34.7          20               3
    ## 2 Con7  #5/4/2/95/2         28.5        44.1          19               5
    ## 3 Con7  #5/5/3/83/3-3       26          41.4          19               6
    ## 4 Con7  #4/2/95/3-3         NA          NA            20               6
    ## 5 Con7  #2/2/95/3-2         NA          NA            20               6
    ## 6 Con7  #1/2/95/2           27          42            19               8
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, pups_survive <dbl>

## `|>` Piping

Piping is a way to streamline code by passing the result of one function
directly as the input to the next function.

``` r
litters_df = 
  read_csv("./data_import_examples/FAS_litters.csv", na = c("NA", ".", "")) |> 
  janitor::clean_names() |> 
  select(-pups_survive) |> 
  mutate(
    wt_gain = gd18_weight - gd0_weight,
    group = str_to_lower(group)) |> 
  drop_na(wt_gain)
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
litters_df
```

    ## # A tibble: 31 × 8
    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ##   <chr> <chr>              <dbl>       <dbl>       <dbl>           <dbl>
    ## 1 con7  #85                 19.7        34.7          20               3
    ## 2 con7  #1/2/95/2           27          42            19               8
    ## 3 con7  #5/5/3/83/3-3       26          41.4          19               6
    ## # ℹ 28 more rows
    ## # ℹ 2 more variables: pups_dead_birth <dbl>, wt_gain <dbl>

- Easiest way to read \|\> is “then”
- Keyboard shortcut: Cmd + Shift + M (Mac)
- Functions in `dplyr` (and much of `tidyverse`) are designed to work
  smoothly with the pipe operator.

For the following e.g, \_ is a placeholder for the piped data. It is
necessary to indicate where the object being piped should go.

``` r
litters_df |>
  lm(wt_gain ~ pups_born_alive, data = _) |>
  broom::tidy()
```

    ## # A tibble: 2 × 5
    ##   term            estimate std.error statistic  p.value
    ##   <chr>              <dbl>     <dbl>     <dbl>    <dbl>
    ## 1 (Intercept)       13.1       1.27      10.3  3.39e-11
    ## 2 pups_born_alive    0.605     0.173      3.49 1.55e- 3

#### Learning Assessment

``` r
pups_df |> 
  janitor::clean_names() |>
  filter(sex == 1) |>
  select(-pd_ears) |>
  mutate(pd_pivot_greater7 = pd_pivot > 7) # performs logical comparison (TRUE or FALSE)
```

    ## # A tibble: 155 × 6
    ##   litter_number   sex pd_eyes pd_pivot pd_walk pd_pivot_greater7
    ##   <chr>         <dbl>   <dbl>    <dbl>   <dbl> <lgl>            
    ## 1 #85               1      13        7      11 FALSE            
    ## 2 #85               1      13        7      12 FALSE            
    ## 3 #1/2/95/2         1      13        7       9 FALSE            
    ## # ℹ 152 more rows

- `|>` vs `%>%`: they are interchangeable. `%>%` has been introduced
  earlier. Thus, `|>`, the newer version, cannot be used in older R
  versions.
