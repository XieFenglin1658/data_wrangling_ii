strings_and_factors
================
Fenglin Xie
2025-10-24

# strings

\##strings–可以是数字，可以是字母，反正就是一串东西 \##对于处理regular
expression很有用，sometimes we do not know the exact match but we know
the general
pattern,like我想去掉data中的字母，但其实不同数据的字母位置可能不同，或者我不知道它在哪里，这个时候就可以用string进行操作

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.4     ✔ readr     2.1.5
    ## ✔ forcats   1.0.1     ✔ stringr   1.5.2
    ## ✔ ggplot2   4.0.0     ✔ tibble    3.3.0
    ## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
    ## ✔ purrr     1.1.0     
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

load key package

``` r
library(rvest)
```

    ## 
    ## Attaching package: 'rvest'

    ## The following object is masked from 'package:readr':
    ## 
    ##     guess_encoding

## Mostly use string vectors

先拿vector做例子，不建立dataframe \##case matters-大写和小写不同

``` r
##创建一个string
string_vec = c("my", "name", "is", "Lin")

##检测某一个item(类似filter)
str_detect(string_vec, "Lin")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
##检测所有包含这个元素的item
str_detect(string_vec, "i")
```

    ## [1] FALSE FALSE  TRUE  TRUE

``` r
##替换某一个item（类似mutate）
str_replace(string_vec,"Lin", "Fenglin")
```

    ## [1] "my"      "name"    "is"      "Fenglin"

``` r
##替换所有包含这个元素的item
str_replace(string_vec, "i", "I")
```

    ## [1] "my"   "name" "Is"   "LIn"

``` r
##remove the item
str_replace(string_vec, "i", "")
```

    ## [1] "my"   "name" "s"    "Ln"

``` r
str_remove(string_vec, "i")
```

    ## [1] "my"   "name" "s"    "Ln"

``` r
string_vec = c(
  "i think we all rule for participating",
  "i think i have been caught",
  "i think this will be quite fun actually",
  "it will be fun, i think"
  )

##检测包含i think的item
str_detect(string_vec, "i think")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
##检测以i think开头的item
str_detect(string_vec, "^i think")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

``` r
##检测以i think结尾的item
str_detect(string_vec, "i think$")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
##去掉以i think结尾的item的i think部分
str_remove(string_vec, "i think$")
```

    ## [1] "i think we all rule for participating"  
    ## [2] "i think i have been caught"             
    ## [3] "i think this will be quite fun actually"
    ## [4] "it will be fun, "

pumpkin themed example \##大小写和有无空格matters！！

``` r
string_vec = c(
  "Time for a Pumpkin Spice Latte!",
  "went to the #pumpkinpatch last weekend",
  "Pumpkin Pie is obviously the best pie",
  "SMASHING PUMPKINS -- LIVE IN CONCERT!!"
  )

str_detect(string_vec, "pumpkin")
```

    ## [1] FALSE  TRUE FALSE FALSE

``` r
##检测包含大写p和小写p的item
str_detect(string_vec, "[Pp]umpkin")
```

    ## [1]  TRUE  TRUE  TRUE FALSE

Let’s get a bit more complicated

``` r
string_vec = c(
  '7th inning stretch',
  '1st half soon to begin. Texas won the toss.',
  'she is 5 feet 4 inches tall',
  '3AM - cant sleep :('
  )

##包含1234567890这些数字的item
str_detect(string_vec, "[1234567890]")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
str_detect(string_vec, "[0-9]")
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
##包含0-9这些数字并且后面紧跟着大写字母A-Z的（中间无空格）
str_detect(string_vec, "[1234567890][A-Z]")
```

    ## [1] FALSE FALSE FALSE  TRUE

``` r
##包含0-9这些数字并且后面紧跟着字母A-Z的,无论大小写（中间无空格）
str_detect(string_vec, "[1234567890][a-zA-Z]")
```

    ## [1]  TRUE  TRUE FALSE  TRUE

``` r
string_vec = c(
  'Its 7:11 in the evening',
  'want to go to 7-11?',
  'my flight is AA711',
  'NetBios: scanning ip 203.167.114.66'
  )

##检测7后面跟着某个element然后后面再跟着一个1
##`.`代表的是anything matches this，是一个特殊符号
str_detect(string_vec,"7.1" )
```

    ## [1] TRUE TRUE TRUE TRUE

``` r
##而如果真的想detect这个element`.`,则需要在前面加上\\
##Some characters are “special”. These include [ and ], ( and ), and .. If you want to search for these, you have to indicate they’re special using \. Unfortunately, \ is also special, so things get weird.
str_detect(string_vec,"7\\.1" )
```

    ## [1] FALSE FALSE FALSE  TRUE

## Factors

\##和string不同，string就是copy了所有的内容，包括引号，而factor是不包括引号的，而且会展示level（in
order：第一个代表female，第二个代表male）

``` r
vec_sex = factor(c("male", "male", "female", "female"))
vec_sex
```

    ## [1] male   male   female female
    ## Levels: female male

``` r
##把这个factor变成numeric的形式
as.numeric(vec_sex)
```

    ## [1] 2 2 1 1

``` r
## level的order默认会按照字母排序，如果想要变成按照数据本身顺序排序的话就用`fct_inorder`
vec_sex = fct_inorder(vec_sex)
vec_sex
```

    ## [1] male   male   female female
    ## Levels: male female

import NSDUH data from the web

``` r
url = "https://samhda.s3-us-gov-west-1.amazonaws.com/s3fs-public/field-uploads/2k15StateFiles/NSDUHsaeShortTermCHG2015.htm"

drug_use_html = read_html(url)
```

This is an “easy” case

``` r
drug_use_html |> 
  html_table()
```

    ## [[1]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "12.90a"         "13.36"          "0.002"        "13.28b"          
    ##  3 "Northea… "13.88a"         "14.66"          "0.005"        "13.98"           
    ##  4 "Midwest" "12.40b"         "12.76"          "0.082"        "12.45"           
    ##  5 "South"   "11.24a"         "11.64"          "0.029"        "12.02"           
    ##  6 "West"    "15.27"          "15.62"          "0.262"        "15.53a"          
    ##  7 "Alabama" "9.98"           "9.60"           "0.426"        "9.90"            
    ##  8 "Alaska"  "19.60a"         "21.92"          "0.010"        "17.30"           
    ##  9 "Arizona" "13.69"          "13.12"          "0.364"        "15.12"           
    ## 10 "Arkansa… "11.37"          "11.59"          "0.678"        "12.79"           
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[2]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "7.96a"          "8.34"           "0.001"        "7.22"            
    ##  3 "Northea… "8.58a"          "9.28"           "0.001"        "7.68"            
    ##  4 "Midwest" "7.50a"          "7.92"           "0.009"        "6.64"            
    ##  5 "South"   "6.74a"          "7.02"           "0.044"        "6.31"            
    ##  6 "West"    "9.84"           "10.08"          "0.324"        "8.85"            
    ##  7 "Alabama" "5.57"           "5.35"           "0.510"        "4.98"            
    ##  8 "Alaska"  "11.85a"         "14.38"          "0.002"        "9.19"            
    ##  9 "Arizona" "8.80"           "8.51"           "0.570"        "8.30"            
    ## 10 "Arkansa… "6.70"           "7.17"           "0.240"        "6.22"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[3]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "1.91"           "1.95"           "0.276"        "5.60"            
    ##  3 "Northea… "2.01"           "2.04"           "0.634"        "5.85b"           
    ##  4 "Midwest" "1.95"           "1.96"           "0.854"        "5.31"            
    ##  5 "South"   "1.69"           "1.75"           "0.137"        "5.18"            
    ##  6 "West"    "2.20"           "2.21"           "0.868"        "6.37b"           
    ##  7 "Alabama" "1.42"           "1.49"           "0.383"        "4.46"            
    ##  8 "Alaska"  "3.01a"          "3.54"           "0.012"        "6.99"            
    ##  9 "Arizona" "2.16"           "2.15"           "0.934"        "6.58"            
    ## 10 "Arkansa… "1.82"           "1.84"           "0.794"        "5.78"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[4]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "1.66a"          "1.76"           "0.040"        "0.60"            
    ##  3 "Northea… "1.94a"          "2.18"           "0.012"        "0.60"            
    ##  4 "Midwest" "1.37"           "1.43"           "0.282"        "0.48"            
    ##  5 "South"   "1.45b"          "1.56"           "0.067"        "0.53"            
    ##  6 "West"    "2.03"           "2.05"           "0.816"        "0.82"            
    ##  7 "Alabama" "1.23"           "1.22"           "0.995"        "0.42"            
    ##  8 "Alaska"  "1.54a"          "2.00"           "0.010"        "0.51"            
    ##  9 "Arizona" "2.25"           "2.29"           "0.861"        "1.01"            
    ## 10 "Arkansa… "0.93"           "1.07"           "0.208"        "0.41"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[5]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "0.30"           "0.33"           "0.217"        "0.12"            
    ##  3 "Northea… "0.43a"          "0.54"           "0.007"        "0.13"            
    ##  4 "Midwest" "0.30"           "0.31"           "0.638"        "0.11"            
    ##  5 "South"   "0.27"           "0.26"           "0.444"        "0.12"            
    ##  6 "West"    "0.25"           "0.29"           "0.152"        "0.13"            
    ##  7 "Alabama" "0.22"           "0.27"           "0.171"        "0.10"            
    ##  8 "Alaska"  "0.70a"          "1.23"           "0.044"        "0.11"            
    ##  9 "Arizona" "0.32a"          "0.55"           "0.001"        "0.17"            
    ## 10 "Arkansa… "0.19"           "0.17"           "0.398"        "0.10"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[6]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "52.42"          "52.18"          "0.337"        "11.55a"          
    ##  3 "Northea… "57.80a"         "56.66"          "0.009"        "13.19"           
    ##  4 "Midwest" "55.14a"         "54.36"          "0.032"        "11.31a"          
    ##  5 "South"   "48.74"          "48.85"          "0.759"        "10.87a"          
    ##  6 "West"    "51.67"          "52.07"          "0.383"        "11.71a"          
    ##  7 "Alabama" "44.72"          "43.94"          "0.533"        "10.53a"          
    ##  8 "Alaska"  "54.02"          "54.98"          "0.444"        "9.22"            
    ##  9 "Arizona" "51.80"          "51.19"          "0.613"        "11.90b"          
    ## 10 "Arkansa… "42.45"          "41.81"          "0.588"        "9.90"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[7]]
    ## # A tibble: 57 × 4
    ##    State    Alcohol Use inPast M…¹ Alcohol Use inPast M…² Alcohol Use inPast M…³
    ##    <chr>    <chr>                  <chr>                  <chr>                 
    ##  1 "NOTE: … "NOTE: State and cens… "NOTE: State and cens… "NOTE: State and cens…
    ##  2 "Total … "22.76a"               "21.57"                "0.000"               
    ##  3 "Northe… "26.11"                "25.98"                "0.792"               
    ##  4 "Midwes… "23.73a"               "22.00"                "0.000"               
    ##  5 "South"  "20.68a"               "19.66"                "0.010"               
    ##  6 "West"   "22.73a"               "21.01"                "0.000"               
    ##  7 "Alabam… "19.25"                "18.19"                "0.305"               
    ##  8 "Alaska" "21.47b"               "23.91"                "0.058"               
    ##  9 "Arizon… "22.01a"               "19.25"                "0.009"               
    ## 10 "Arkans… "18.07"                "16.65"                "0.106"               
    ## # ℹ 47 more rows
    ## # ℹ abbreviated names: ¹​`Alcohol Use inPast Month(2013-2014)`,
    ## #   ²​`Alcohol Use inPast Month(2014-2015)`,
    ## #   ³​`Alcohol Use inPast Month(P Value)`
    ## 
    ## [[8]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "25.36a"         "24.56"          "0.000"        "7.42a"           
    ##  3 "Northea… "23.76"          "23.30"          "0.216"        "7.18a"           
    ##  4 "Midwest" "28.57a"         "27.39"          "0.000"        "8.58a"           
    ##  5 "South"   "26.91a"         "26.24"          "0.034"        "7.57a"           
    ##  6 "West"    "21.20a"         "20.29"          "0.015"        "6.31a"           
    ##  7 "Alabama" "31.62"          "30.46"          "0.295"        "8.43"            
    ##  8 "Alaska"  "29.30a"         "31.51"          "0.048"        "10.73"           
    ##  9 "Arizona" "22.14"          "22.51"          "0.678"        "6.81"            
    ## 10 "Arkansa… "35.57"          "34.05"          "0.188"        "10.89"           
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[9]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "21.05a"         "20.12"          "0.000"        "5.24a"           
    ##  3 "Northea… "19.69"          "19.13"          "0.101"        "5.10a"           
    ##  4 "Midwest" "23.84a"         "22.50"          "0.000"        "6.16a"           
    ##  5 "South"   "22.37a"         "21.41"          "0.001"        "5.22a"           
    ##  6 "West"    "17.43a"         "16.66"          "0.026"        "4.56a"           
    ##  7 "Alabama" "25.90"          "24.25"          "0.106"        "5.66"            
    ##  8 "Alaska"  "22.00a"         "24.64"          "0.008"        "6.15"            
    ##  9 "Arizona" "18.73"          "19.23"          "0.576"        "4.95"            
    ## 10 "Arkansa… "28.51"          "27.81"          "0.519"        "7.13"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[10]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "6.50a"          "6.14"           "0.001"        "2.76"            
    ##  3 "Northea… "6.64"           "6.39"           "0.193"        "2.88"            
    ##  4 "Midwest" "6.59a"          "6.25"           "0.033"        "2.70"            
    ##  5 "South"   "6.20a"          "5.76"           "0.003"        "2.65"            
    ##  6 "West"    "6.80"           "6.47"           "0.120"        "2.91"            
    ##  7 "Alabama" "5.76a"          "4.64"           "0.007"        "2.84b"           
    ##  8 "Alaska"  "6.72"           "7.43"           "0.153"        "2.12"            
    ##  9 "Arizona" "7.60b"          "6.66"           "0.062"        "3.37"            
    ## 10 "Arkansa… "5.23"           "4.88"           "0.347"        "2.63"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[11]]
    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "3.04"           "2.97"           "0.321"        "1.01"            
    ##  3 "Northea… "3.01"           "3.09"           "0.500"        "1.02"            
    ##  4 "Midwest" "3.06b"          "2.86"           "0.061"        "1.03"            
    ##  5 "South"   "2.92a"          "2.73"           "0.047"        "0.96"            
    ##  6 "West"    "3.25"           "3.37"           "0.409"        "1.05"            
    ##  7 "Alabama" "2.99a"          "2.34"           "0.026"        "0.98"            
    ##  8 "Alaska"  "3.21"           "3.67"           "0.200"        "0.77"            
    ##  9 "Arizona" "3.44"           "3.62"           "0.591"        "1.11"            
    ## 10 "Arkansa… "2.73"           "2.42"           "0.255"        "0.96"            
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>
    ## 
    ## [[12]]
    ## # A tibble: 57 × 10
    ##    State     `18+(2013-2014)` `18+(2014-2015)` `18+(P Value)` `18-25(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "4.15"           "4.05"           "0.325"        "4.52a"           
    ##  3 "Northea… "3.93"           "3.94"           "0.953"        "4.67a"           
    ##  4 "Midwest" "4.45"           "4.36"           "0.511"        "4.93a"           
    ##  5 "South"   "4.17"           "4.00"           "0.206"        "4.18a"           
    ##  6 "West"    "4.02"           "3.96"           "0.681"        "4.56b"           
    ##  7 "Alabama" "4.53"           "4.64"           "0.749"        "4.30"            
    ##  8 "Alaska"  "3.90"           "4.02"           "0.707"        "4.60a"           
    ##  9 "Arizona" "4.09"           "4.33"           "0.491"        "4.45"            
    ## 10 "Arkansa… "5.24"           "5.27"           "0.942"        "4.49"            
    ## # ℹ 47 more rows
    ## # ℹ 5 more variables: `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>
    ## 
    ## [[13]]
    ## # A tibble: 57 × 10
    ##    State     `18+(2013-2014)` `18+(2014-2015)` `18+(P Value)` `18-25(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "18.29"          "18.01"          "0.146"        "19.75a"          
    ##  3 "Northea… "17.87"          "17.76"          "0.735"        "20.80a"          
    ##  4 "Midwest" "18.61"          "18.34"          "0.356"        "20.45a"          
    ##  5 "South"   "18.01"          "17.82"          "0.519"        "18.22a"          
    ##  6 "West"    "18.79b"         "18.18"          "0.088"        "20.70a"          
    ##  7 "Alabama" "19.51"          "18.85"          "0.469"        "18.09"           
    ##  8 "Alaska"  "18.12"          "18.11"          "0.989"        "20.33a"          
    ##  9 "Arizona" "18.59"          "18.32"          "0.756"        "18.76"           
    ## 10 "Arkansa… "20.00"          "19.77"          "0.816"        "19.99"           
    ## # ℹ 47 more rows
    ## # ℹ 5 more variables: `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>
    ## 
    ## [[14]]
    ## # A tibble: 57 × 10
    ##    State     `18+(2013-2014)` `18+(2014-2015)` `18+(P Value)` `18-25(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "3.94"           "3.99"           "0.526"        "7.44a"           
    ##  3 "Northea… "3.81"           "3.93"           "0.407"        "7.60b"           
    ##  4 "Midwest" "4.11"           "4.14"           "0.791"        "7.83"            
    ##  5 "South"   "3.84"           "3.86"           "0.896"        "6.89a"           
    ##  6 "West"    "4.02"           "4.12"           "0.522"        "7.84"            
    ##  7 "Alabama" "3.98"           "4.02"           "0.885"        "7.31"            
    ##  8 "Alaska"  "4.21"           "4.68"           "0.237"        "8.30b"           
    ##  9 "Arizona" "4.23"           "4.34"           "0.775"        "7.04"            
    ## 10 "Arkansa… "4.58"           "4.41"           "0.682"        "6.67"            
    ## # ℹ 47 more rows
    ## # ℹ 5 more variables: `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>
    ## 
    ## [[15]]
    ## # A tibble: 57 × 13
    ##    State     `18+(2013-2014)` `18+(2014-2015)` `18+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "6.63"           "6.64"           "0.915"        "11.01a"          
    ##  3 "Northea… "6.66"           "6.82"           "0.458"        "10.63a"          
    ##  4 "Midwest" "6.81"           "6.87"           "0.736"        "10.81a"          
    ##  5 "South"   "6.47"           "6.52"           "0.750"        "10.77a"          
    ##  6 "West"    "6.67"           "6.47"           "0.353"        "11.82a"          
    ##  7 "Alabama" "6.85"           "6.81"           "0.948"        "10.74"           
    ##  8 "Alaska"  "6.57"           "6.73"           "0.770"        "9.92a"           
    ##  9 "Arizona" "7.32"           "6.77"           "0.362"        "13.23"           
    ## 10 "Arkansa… "7.31"           "7.78"           "0.446"        "11.95"           
    ## # ℹ 47 more rows
    ## # ℹ 8 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>

一共15个table，若只要第一个table

``` r
drug_use_html |> 
  html_table() |> 
  first()
```

    ## # A tibble: 57 × 16
    ##    State     `12+(2013-2014)` `12+(2014-2015)` `12+(P Value)` `12-17(2013-2014)`
    ##    <chr>     <chr>            <chr>            <chr>          <chr>             
    ##  1 "NOTE: S… "NOTE: State an… "NOTE: State an… "NOTE: State … "NOTE: State and …
    ##  2 "Total U… "12.90a"         "13.36"          "0.002"        "13.28b"          
    ##  3 "Northea… "13.88a"         "14.66"          "0.005"        "13.98"           
    ##  4 "Midwest" "12.40b"         "12.76"          "0.082"        "12.45"           
    ##  5 "South"   "11.24a"         "11.64"          "0.029"        "12.02"           
    ##  6 "West"    "15.27"          "15.62"          "0.262"        "15.53a"          
    ##  7 "Alabama" "9.98"           "9.60"           "0.426"        "9.90"            
    ##  8 "Alaska"  "19.60a"         "21.92"          "0.010"        "17.30"           
    ##  9 "Arizona" "13.69"          "13.12"          "0.364"        "15.12"           
    ## 10 "Arkansa… "11.37"          "11.59"          "0.678"        "12.79"           
    ## # ℹ 47 more rows
    ## # ℹ 11 more variables: `12-17(2014-2015)` <chr>, `12-17(P Value)` <chr>,
    ## #   `18-25(2013-2014)` <chr>, `18-25(2014-2015)` <chr>, `18-25(P Value)` <chr>,
    ## #   `26+(2013-2014)` <chr>, `26+(2014-2015)` <chr>, `26+(P Value)` <chr>,
    ## #   `18+(2013-2014)` <chr>, `18+(2014-2015)` <chr>, `18+(P Value)` <chr>

table的底部备注会被放在table的第一行，现在我们想要去掉这一行
\##filter是针对variable，slice是针对rows

``` r
ndsuh_df = 
  drug_use_html |> 
  html_table() |> 
  first() |> 
  slice(-1)
```
