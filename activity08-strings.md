Activity 8
================
Name

## Data and packages

Load the entirety of the `{tidyverse}`. You will do a little work with
dates in this activity so also load `{lubridate}`. Be sure to avoid
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

-   [`{stringr}`](https://stringr.tidyverse.org/index.html)
-   [`{forcats}`](https://forcats.tidyverse.org/)

Using `here::here`, upload the `billboard_songs.txt` file that is saved
in your `data/` folder. Notice that this file is a tab-delimited file
that is stored as a `.txt` file. Therefore, you will need to use either
`read_delim` with a `delim = ...` argument or (better) `read_tsv`.
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

This is a long dataset (34,605 observations)!

## Analysis

### Cleaning date

When you look at `chart date` you’ll notice that it was read in as a
numeric (`<dbl>`) variable type. What does the format of the date appear
to be in?

``` r
head(df$`chart date`,10)
```

    ##  [1] 20150307 20150307 20150307 20150307 20150307 20150307 20150307 20150307
    ##  [9] 20150307 20150307

**Response**: yyyymmdd, and may need to change to character if using
stringr

In this activity, we will be using `str_...` functions to split or find
patterns in strings of information. Do the following with your dataset:

-   *Create* a variable called `date` that treats `chart date` *as a
    character* variable type,
-   Use `stringr::str_sub` on `date` to create three new columns:
    `year`, `month`, `day`
-   Replace/overwrite the variable `date` with an updated format using
    `lubridate::make_date`.

Once you have verified that this worked, overwrite your data object.

``` r
df$date = as.character(df$`chart date`)
df$y = str_sub(df$date,1,4)
df$m = str_sub(df$date,5,6)
df$d = str_sub(df$date,7,8)
```

``` r
df$date2 = lubridate::as_date(df$date)
```

#### Analyzing using dates

What 10 songs spent the longest on the charts? Give only the title,
artists, and weeks.

``` r
df$weeks=df$`weeks on chart`
df%>%
  select('title','artist','weeks')%>%
  slice_max(order_by = weeks,n=10)
```

    ## # A tibble: 10 x 3
    ##    title                              artist                               weeks
    ##    <chr>                              <chr>                                <dbl>
    ##  1 Radioactive                        IMAGINE DRAGONS                         85
    ##  2 I'm Yours                          JASON MRAZ                              76
    ##  3 How Do I Live                      LeANN RIMES                             69
    ##  4 Counting Stars                     ONEREPUBLIC                             68
    ##  5 Party Rock Anthem                  LMFAO featuring LAUREN BENNETT & GO…    67
    ##  6 Foolish Games / You Were Meant Fo… JEWEL                                   65
    ##  7 Rolling In The Deep                ADELE                                   64
    ##  8 Before He Cheats                   CARRIE UNDERWOOD                        64
    ##  9 Ho Hey                             THE LUMINEERS                           62
    ## 10 Need You Now                       LADY ANTEBELLUM                         60

**Response**:

``` r
library(lubridate)
```

    ## 
    ## Attaching package: 'lubridate'

    ## The following objects are masked from 'package:base':
    ## 
    ##     date, intersect, setdiff, union

What date did the oldest song(s) in this dataset leave the charts? Give
only the *distinct* dates.

``` r
df$date3=as.numeric(df$date2)
df$date4=df$date3+7*df$weeks
df$dateOut = lubridate::as_date(df$date4)
```

``` r
df%>%
  select('title','artist','dateOut')%>%
  slice_min(order_by = df$date4,n=10)
```

    ## # A tibble: 10 x 3
    ##    title                          artist                              dateOut   
    ##    <chr>                          <chr>                               <date>    
    ##  1 Playmates                      KAY KYSER & HIS ORCHESTRA / SULLY … 1940-07-27
    ##  2 Where Was I?                   CHARLIE BARNETT & HIS ORCHESTRA / … 1940-07-27
    ##  3 Imagination                    TOMMY DORSEY & HIS ORCHESTRA / FRA… 1940-07-27
    ##  4 Make-Believe Island            MITCHELL AYERS & HIS ORCHESTRA / M… 1940-07-27
    ##  5 Make-Believe Island            JAN SAVITT & HIS ORCHESTRA & BON B… 1940-08-03
    ##  6 Imagination                    GLENN MILLER & HIS ORCHESTRA / RAY… 1940-08-24
    ##  7 When The Swallows Come Back T… GLENN MILLER & HIS ORCHESTRA / RAY… 1940-08-31
    ##  8 God Bless America              KATE SMITH                          1940-09-14
    ##  9 Dolemite                       ERSKINE HAWKINS & HIS ORCHESTRA     1940-09-14
    ## 10 6 Lessons From Madame La Zonga JIMMY DORSEY & HIS ORCHESTRA / HEL… 1940-09-14

**Response**:

### Artists who work together

Before we get to far, let’s create common definitions:

In the string below, Nicki Minaj and Young Thug are considered to be
**featured**.

    RAE SREMMURD featuring NICKI MINAJ & YOUNG THUG

In the string below, Jessie J and Ariana Grande and Nicki Minaj are all
considered to have **collaborated** on the song.

    JESSIE J, ARIANA GRANDE & NICKI MINAJ

Which artist has been **featured** on the most Billboard charting songs?

First, create a vector object called `artist_credentials` that takes
your dataset and *then* `pull`s only the `artist` column. The `pull`
function is like `select` except that it outputs a vector rather than a
data frame (tibble). Using this function will help us avoid many
“`coercing`” warnings - I find it easier to work with vectors of
strings.

``` r
try=df %>%
  select(artist)%>%
  filter(str_detect(artist,'featuring'))
```

&lt;&lt;&lt;&lt;&lt;&lt;&lt; HEAD

``` r
try2=df %>%
  select(artist)%>%
  filter(str_detect(artist,'featuring'))%>%
  pull(artist)
```

``` r
get_after_period <- function(my_vector) {    

        # Return a string vector without the characters
        # before a period (excluding the period)

        # my_vector, a string vector

        str_sub(my_vector, str_locate(my_vector, "\\.")[,1]+1) 

        }
```

``` r
a=try2[1]
a
```

    ## [1] "MARK RONSON featuring BRUNO MARS"

``` r
b=str_replace_all(a,"featuring",".")
get_after_period(b)
```

    ## [1] " BRUNO MARS"

``` r
b=str_replace_all(try2,"featuring",".")
try3=get_after_period(b)
try3[1:10]
```

    ##  [1] " BRUNO MARS"                     " JUICY J"                       
    ##  [3] " DRAKE, LIL WAYNE & CHRIS BROWN" " DRAKE & LIL WAYNE"             
    ##  [5] " JUICY J"                        " E-40"                          
    ##  [7] " JEREMIH"                        " SAGE THE GEMINI & LOOKAS"      
    ##  [9] " ELLIE GOULDING"                 " ASHLEY MONROE"

=======

``` r
try2=df %>%
  select(artist)%>%
  filter(str_detect(artist,'featuring'))%>%
  pull(artist)
```

Using the `artist_credentials` vector, do the following. I recommend
that you do one bullet at time and verify that it worked.
&gt;&gt;&gt;&gt;&gt;&gt;&gt; 05f51ba58ee3e0a0dc6cf0b25372f84c759f778b

-   Use `str_split` to separate entries with multiple featured artist -
    these could include `,`, `&`, `or` (note the whitespace), or `and`
    (note the whitespace) (this should produce a list), *then*
-   Use `unlist` to lengthen all featured artists into one vector
    (compare the output here to the output from the previous bullet
    point), *then*
-   Use `str_trim` to remove whitespace from `"both"` sides of each
    entry.

``` r
try4 = str_split(try3,'[,|&|or|and]')
try5 = str_trim(try4,side=c("both"))
```

    ## Warning in stri_trim_both(string): argument is not an atomic vector; coercing

``` r
try6=str_count(try5)
```

``` r
df1 = data.frame(try5,try6)
df1%>%
  select(try5,try6)%>%
  slice_max(order_by = try6,n=5)
```

    ##                                                         try5 try6
    ## 1 c("I.AM . MILEY CYRUS", " FRENCH MONTANA", " WIZ KHALIFA")   58
    ## 2  c(" CHRIS BROWN", " TYGA", " WIZ KHALIFA ", " LIL WAYNE")   57
    ## 3   c(" LIL WAYNE ", " FRENCH MONTANA ", "", " ", " TOO SH")   56
    ## 4   c(" LIL WAYNE", " WIZ KHALIFA", " T.I. ", " ANDRE 3000")   56
    ## 5   c(" STEADY MOBB'N", " MIA X", " MO B. DICK ", " O'DELL")   56
