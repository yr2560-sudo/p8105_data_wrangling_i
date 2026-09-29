Tidy Data
================
Yiman Rui
2026-09-29

This file is for doing data tidying.

``` r
library(tidyverse)
```

    ## Warning: package 'tibble' was built under R version 4.5.2

    ## Warning: package 'dplyr' was built under R version 4.5.2

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.2.1     ✔ readr     2.1.5
    ## ✔ forcats   1.0.1     ✔ stringr   1.6.0
    ## ✔ ggplot2   4.0.0     ✔ tibble    3.3.1
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

## Let’s tidy some data!

``` r
pulse_df = 
  haven::read_sas("~/Desktop/Data Science/Data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names()
```

Okay, let’s tidy!

``` r
pulse_tidy_df =
  pulse_df |> 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi_score"
  ) |> 
  mutate(
    visit = replace(visit, visit == "bl", "00m")
  )
```

Just use once ..

``` r
pulse_df = 
  haven::read_sas("~/Desktop/Data Science/Data/public_pulse_data.sas7bdat") |> 
  janitor::clean_names() |> 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi_score"
  ) |> 
  mutate(
    visit = replace(visit, visit == "bl", "00m")
  )
```

Let’s practice!

Import the litters data; keep columns litter number and GD weights; and
tidy.

``` r
litters_df = 
  read_csv("~/Desktop/Data Science/Data/FAS_litters.csv", na = c(".", "", "NA")) |> 
  janitor::clean_names() |> 
  select(litter_number, gd0_weight, gd18_weight) |> 
  pivot_longer(
    gd0_weight:gd18_weight,
    names_to = "gd", 
    values_to = "weight"
  ) |> 
  mutate(
    gd = case_match(
      gd, 
      "gd0_weight"  ~ 0, 
      "gd18_weight" ~ 18, 
    )
  )
```

    ## Rows: 49 Columns: 8
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `gd = case_match(gd, "gd0_weight" ~ 0, "gd18_weight" ~ 18, )`.
    ## Caused by warning:
    ## ! `case_match()` was deprecated in dplyr 1.2.0.
    ## ℹ Please use `recode_values()` instead.

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

## bind some rows

``` r
fellowship_df = 
  readxl::read_excel("~/Desktop/Data Science/Data/LotR_Words.xlsx", range = "B3:D6") |>
  mutate(movie = "fellowship")

two_towers_df = 
  readxl::read_excel("~/Desktop/Data Science/Data/LotR_Words.xlsx", range = "F3:H6") |>
  mutate(movie = "two towers")

return_df = 
  readxl::read_excel("~/Desktop/Data Science/Data/LotR_Words.xlsx", range = "J3:L6") |>
  mutate(movie = "return of the king")
```

Pull all together and tidy

``` r
lotr_tidy = 
  bind_rows(fellowship_df, two_towers_df, return_df) |>
  janitor::clean_names() |>
  relocate(movie) |>
  pivot_longer(
    female:male,
    names_to = "gender",
    values_to = "words"
  )
```
