Visualization part 2
================
Diana Hernandez
2023-10-03

## Setting options

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
knitr::opts_chunk$set(
  fig.width = 6,
  fig.asp = .6,
  out.width = "90%"
)

theme_set(theme_minimal() + theme(legend.position = "bottom"))

options(
  ggplot2.continuous.colour = "viridis",
  ggplot2.continuous.fill = "viridis"
)

scale_colour_discrete = scale_colour_viridis_d
scale_fill_discrete = scale_fill_viridis_d
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2023-12-31") |>
  mutate(
    name = recode(
      id, 
      USW00094728 = "CentralPark_NY", 
      USW00022534 = "Molokai_HI",
      USS0023B17S = "Waterhole_WA"),
    tmin = tmin / 10,
    tmax = tmax / 10) |>
  select(name, id, everything())
```

    ## using cached file: /Users/dianahernandez/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-09-28 10:50:50.364706 (8.524)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/dianahernandez/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-09-28 10:51:01.830547 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/dianahernandez/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-09-28 10:51:05.860357 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

## Same plot from last time

Can add filters, mutate the data instead of editting the graph itself

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color=name)) + 
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations; data from rnoaa"
  ) +
  scale_x_continuous(
    breaks = c(-15, 0, 15),
    labels = c("-15 C", "0", "15")
  ) +
  scale_y_continuous(
    position = "right",
    trans = "sqrt",
    limits = c(0, 30)
  )
```

    ## Warning in self$trans$transform(x): NaNs produced

    ## Warning: Transformation introduced infinite values in continuous y-axis

    ## Warning: Removed 456 rows containing missing values (`geom_point()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-3-1.png" width="90%" />

what about colors…

continuous variables handled differently than categorical

scale_color_hue() works for continuous data

viridis vs ggplot color palette (generally use viridis color palette)

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color=name)) + 
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations; data from rnoaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE)
```

    ## Warning: Removed 33 rows containing missing values (`geom_point()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-4-1.png" width="90%" />

## Themes

order matters for themes

theme_bw() resets entire plot in terms of themes, should go first

also theme_classic for no gridlines

theme_minimal too

``` r
weather_df |> 
  ggplot(aes(x = tmin, y = tmax, color=name)) + 
  geom_point(alpha = .5) +
  labs(
    title = "Temperature plot",
    x = "Min daily temp (Degrees C)",
    y = "Max daily temp",
    color = "Location",
    caption = "Max vs min daily temp in three locations; data from rnoaa"
  ) + 
  viridis::scale_color_viridis(discrete = TRUE) + 
  theme_bw() +
  theme(legend.position = "bottom") 
```

    ## Warning: Removed 33 rows containing missing values (`geom_point()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-5-1.png" width="90%" />

## Data argument..

if you put it in ggplot, it will assume to put it in geom_point() and
geom_smoth()

``` r
weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) + 
  geom_point() +
  geom_smooth()
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 33 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 33 rows containing missing values (`geom_point()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-6-1.png" width="90%" />

``` r
nyc_weather_df = 
   weather_df |> 
  filter(name == "CentralPark_NY")

hawaii_weather_df = 
  weather_df |> 
  filter(name == "Molokai_HI")


ggplot(nyc_weather_df, aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_line(data = hawaii_weather_df)
```

    ## Warning: Removed 5 rows containing missing values (`geom_point()`).

    ## Warning: Removed 5 rows containing missing values (`geom_line()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-6-2.png" width="90%" />

## ‘patchwork’

facet allows to make visual comparisons

``` r
 weather_df |>  
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  facet_grid(. ~ name)
```

    ## Warning: Removed 33 rows containing missing values (`geom_point()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-7-1.png" width="90%" />

``` r
ggp_temp_scatter =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point(alpha = .5) + 
  theme(legend.positon = "none")

ggp_prcp_density = 
  weather_df |> 
  filter(prcp > 25) |> 
  ggplot(aes(x = prcp, fill = name)) +
  geom_density(alpha = .5) +
  theme(legend.positon = "none")

ggp_tmax_date =
  weather_df |> 
  ggplot(aes(x = date, y = tmax, color = name)) +
  geom_point() +
  geom_smooth(se = FALSE) +
  theme(legend.position = "bottom")
```

## data manipulation

convert to factor for alphabetical order

``` r
weather_df |> 
  mutate(
    name = fct_relevel(name, c("Molokai_HI", "CentralPark_NY", "Waterhole_WA"))
  ) |> 
  ggplot(aes(x = name, y = tmax)) +
  geom_boxplot()
```

    ## Warning: Removed 33 rows containing non-finite values (`stat_boxplot()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-9-1.png" width="90%" />

``` r
weather_df |> 
  mutate(
    name = fct_reorder(name, tmax)
  ) |> 
  ggplot(aes(x = name, y = tmax, fill = name)) +
  geom_violin()
```

    ## Warning: There was 1 warning in `mutate()`.
    ## ℹ In argument: `name = fct_reorder(name, tmax)`.
    ## Caused by warning:
    ## ! `fct_reorder()` removing 33 missing values.
    ## ℹ Use `.na_rm = TRUE` to silence this message.
    ## ℹ Use `.na_rm = FALSE` to preserve NAs.

    ## Warning: Removed 33 rows containing non-finite values (`stat_ydensity()`).

<img src="visualization_part_2_files/figure-gfm/unnamed-chunk-9-2.png" width="90%" />
