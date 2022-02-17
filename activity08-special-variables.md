Activity 8
================
Name

## Data and packages

Load the entirety of the `{tidyverse}`. `{forcats}` and `{stringr}` are
loaded as part of this. If you wish to work with dates during this
activity, you will need to also load `{lubridate}`. Be sure to avoid
printing out any unnecessary information and give the code chunk a
meaningful name.

``` r
library(tidyverse)
```

    ## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──

    ## ✓ ggplot2 3.3.4     ✓ purrr   0.3.4
    ## ✓ tibble  3.1.2     ✓ dplyr   1.0.7
    ## ✓ tidyr   1.1.3     ✓ stringr 1.4.0
    ## ✓ readr   1.4.0     ✓ forcats 0.5.1

    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

Cheatsheets that you might want to add to your collection:

-   [`{stringr}`](https://stringr.tidyverse.org/)
-   [`{forcats}`](https://forcats.tidyverse.org/)
-   [`{lubridate}`](https://lubridate.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv` .
Assign the file to a meaningful object name, be sure to avoid printing
any unnecessary information, and give the code chunk a meaningful name.

``` r
setwd("~/STA518/Preps/activity08-special-variables")
df = read_tsv(here::here('data','billboard_songs.txt'))
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   title = col_character(),
    ##   artist = col_character(),
    ##   `overall peak` = col_double(),
    ##   `weeks on chart` = col_double(),
    ##   `chart date` = col_double()
    ## )

These data include information on song popularity. In the US, the
Billboard Hot 100 is a list that comes out every week, showing the 100
most played songs that week. More information about the creation of this
dataset, as well as some analyses by the author, can be found here:
<https://mikekling.com/analyzing-the-billboard-hot-100/>. The dataset
you are provided is a limited version of the full data, containing:

-   `title`
-   `artist`
-   `overall peak`: The highest rank the song ever reached (1 is the
    best)
-   `weeks on chart`: The number of weeks the song was on the chart
-   `chart date`: The latest date the song appeared on the Billboard Hot
    100

This is a long dataset (34,605 observations)! You might like to create a
small dataset with only, say, 200 of the rows to try all your code out
on the smaller dataset first, and then only run the analysis of the full
data after you have experienced everything. One way to do this is to use
a function like `slice_sample(n = ...)` from `{dplyr}`.

``` r
# Randomly sample 200 rows
d=df %>% 
  slice_sample(n = 5)
```

If you wish to work with `{stringr}`, I find it useful to work on
vectors first. A way to do this is with `pull` from `{dplyr}`.

``` r
# Turn variable from dataset into a vector
d %>% 
  pull('artist')
```

    ## [1] "THE FINATTICZ"               "DION"                       
    ## [3] "NEIL YOUNG with CRAZY HORSE" "THE ADC BAND"               
    ## [5] "SPANKY AND OUR GANG"

## Analysis

You are encouraged to explore these data as you wish using functions
from the packages for the three special variable types. Some ideas that
you might be interested in:

-   What 10 songs (display title, artist, and week) were on the charts
    for the longest?

``` r
a = d$`weeks on chart`
d%>%
  slice_max(order_by = a, n = 2)
```

    ## # A tibble: 2 x 5
    ##   title       artist                `overall peak` `weeks on chart` `chart date`
    ##   <chr>       <chr>                          <dbl>            <dbl>        <dbl>
    ## 1 The Wander… DION                               2               18     19620407
    ## 2 Cinnamon G… NEIL YOUNG with CRAZ…             55               11     19700814

-   What distinct date did the oldest song(s) leave the charts?

``` r
a = d$`chart date`
d%>%
  slice_min(order_by = a, n = 1)
```

    ## # A tibble: 1 x 5
    ##   title        artist `overall peak` `weeks on chart` `chart date`
    ##   <chr>        <chr>           <dbl>            <dbl>        <dbl>
    ## 1 The Wanderer DION                2               18     19620407

Assuming you were 16 years old by 2015. - Which artist has been
**featured** on the most Billboard charting songs? - Which artist has
**collaborated** on the most Billboard charting songs? - Create some
data visualization controlling the order of the character/string
variables.

``` r
try=df %>%
  select(title,artist,'weeks on chart')%>%
  filter(str_detect(artist,'featuring'))%>%
  separate_rows(artist)%>%
  pull(artist)
```

``` r
a=tibble(artist = try) %>% 
  group_by(artist) %>% 
  summarise(n = n())
a%>%
  slice_max(order_by = n,n=4)
```

    ## # A tibble: 4 x 2
    ##   artist        n
    ##   <chr>     <int>
    ## 1 featuring  1527
    ## 2 THE         201
    ## 3 LIL         144
    ## 4 WAYNE        97

## Attribution

Parts of this Activity are based on a lab from [Dr. Kelly
Bodwin’s](https://www.kelly-bodwin.com/) STAT 331 course.
