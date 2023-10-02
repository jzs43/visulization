Visualization
================

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
library(ggridges)
```

``` r
weather_df = 
  rnoaa::meteo_pull_monitors(
    c("USW00094728", "USW00022534", "USS0023B17S"),
    var = c("PRCP", "TMIN", "TMAX"), 
    date_min = "2021-01-01",
    date_max = "2022-12-31") |>
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

    ## using cached file: /Users/jiangzheshu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00094728.dly

    ## date created (size, mb): 2023-10-02 00:33:00.465025 (8.525)

    ## file min/max dates: 1869-01-01 / 2023-09-30

    ## using cached file: /Users/jiangzheshu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USW00022534.dly

    ## date created (size, mb): 2023-10-02 00:33:05.808979 (3.83)

    ## file min/max dates: 1949-10-01 / 2023-09-30

    ## using cached file: /Users/jiangzheshu/Library/Caches/org.R-project.R/R/rnoaa/noaa_ghcnd/USS0023B17S.dly

    ## date created (size, mb): 2023-10-02 00:33:07.742436 (0.994)

    ## file min/max dates: 1999-09-01 / 2023-09-30

``` r
weather_df
```

    ## # A tibble: 2,190 × 6
    ##    name           id          date        prcp  tmax  tmin
    ##    <chr>          <chr>       <date>     <dbl> <dbl> <dbl>
    ##  1 CentralPark_NY USW00094728 2021-01-01   157   4.4   0.6
    ##  2 CentralPark_NY USW00094728 2021-01-02    13  10.6   2.2
    ##  3 CentralPark_NY USW00094728 2021-01-03    56   3.3   1.1
    ##  4 CentralPark_NY USW00094728 2021-01-04     5   6.1   1.7
    ##  5 CentralPark_NY USW00094728 2021-01-05     0   5.6   2.2
    ##  6 CentralPark_NY USW00094728 2021-01-06     0   5     1.1
    ##  7 CentralPark_NY USW00094728 2021-01-07     0   5    -1  
    ##  8 CentralPark_NY USW00094728 2021-01-08     0   2.8  -2.7
    ##  9 CentralPark_NY USW00094728 2021-01-09     0   2.8  -4.3
    ## 10 CentralPark_NY USW00094728 2021-01-10     0   5    -1.6
    ## # ℹ 2,180 more rows

scatterplots! create my first scatterplot ever

``` r
ggplot(weather_df,aes(x=tmin,y=tmax))+geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->

new approach, same plot

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax))+geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-3-1.png)<!-- -->

save and edit a plot object

``` r
weather_plot=
  weather_df %>% 
  ggplot(aes(x=tmin,y=tmax))
weather_plot+geom_point()
```

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-4-1.png)<!-- -->

# advanced scatterplot

start with the same one and make it fancy

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name))+geom_point()+geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-5-1.png)<!-- -->

what about aes.. statement?

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax))+geom_point(aes(color=name))+geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'gam' and formula = 'y ~ s(x, bs = "cs")'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-6-1.png)<!-- --> facet

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name,alpha=tmin))+geom_point()+geom_smooth(se=FALSE)+facet_grid(. ~name)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

    ## Warning: The following aesthetics were dropped during statistical transformation: alpha
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: alpha
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?
    ## The following aesthetics were dropped during statistical transformation: alpha
    ## ℹ This can happen when ggplot fails to infer the correct grouping structure in
    ##   the data.
    ## ℹ Did you forget to specify a `group` aesthetic or to convert a numerical
    ##   variable into a factor?

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-7-1.png)<!-- --> \## some samll
notes

how many geoms have to exist

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax,color=name))+
  geom_smooth(se=FALSE)
```

    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

    ## Warning: Removed 17 rows containing non-finite values (`stat_smooth()`).

![](vis_files/figure-gfm/unnamed-chunk-8-1.png)<!-- -->

you can use a neat geom!

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=tmax))+
  geom_density2d()+
  geom_point(alpha=0.3)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density2d()`).

    ## Warning: Removed 17 rows containing missing values (`geom_point()`).

![](vis_files/figure-gfm/unnamed-chunk-9-1.png)<!-- -->

## Univaribale plots

hitogram are really good

``` r
weather_df %>% 
  ggplot(aes(x=tmin))+
  geom_histogram()
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](vis_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

can we add color…

``` r
weather_df %>% 
  ggplot(aes(x=tmin,fill=name))+
  geom_histogram(position="dodge")
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 17 rows containing non-finite values (`stat_bin()`).

![](vis_files/figure-gfm/unnamed-chunk-11-1.png)<!-- -->

lets try a new geometry

``` r
weather_df %>% 
  ggplot(aes(x=tmin,fill=name))+
  geom_density(alpha=0.3)
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_density()`).

![](vis_files/figure-gfm/unnamed-chunk-12-1.png)<!-- -->

what about box plot

``` r
weather_df %>% 
  ggplot(aes(x=name,y=tmax))+
  geom_boxplot()
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_boxplot()`).

![](vis_files/figure-gfm/unnamed-chunk-13-1.png)<!-- -->

trendy plots

``` r
weather_df %>% 
  ggplot(aes(x=name,y=tmin,fill=name))+
  geom_violin()+
  stat_summary(fun="median")
```

    ## Warning: Removed 17 rows containing non-finite values (`stat_ydensity()`).

    ## Warning: Removed 17 rows containing non-finite values (`stat_summary()`).

    ## Warning: Removed 3 rows containing missing values (`geom_segment()`).

![](vis_files/figure-gfm/unnamed-chunk-14-1.png)<!-- -->

ridge plot

``` r
weather_df %>% 
  ggplot(aes(x=tmin,y=name))+
  geom_density_ridges()
```

    ## Picking joint bandwidth of 1.41

    ## Warning: Removed 17 rows containing non-finite values
    ## (`stat_density_ridges()`).

![](vis_files/figure-gfm/unnamed-chunk-15-1.png)<!-- -->
