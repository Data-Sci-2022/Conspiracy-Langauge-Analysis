Data wrangling
================

-   <a href="#taking-a-look-inside-the-dataframes"
    id="toc-taking-a-look-inside-the-dataframes">Taking a look inside the
    dataframes</a>
-   <a href="#data-wrangling" id="toc-data-wrangling">Data wrangling</a>
-   <a href="#joining-the-dataframes"
    id="toc-joining-the-dataframes">Joining the dataframes</a>

Continued from [Data import](data_import.md).

## Taking a look inside the dataframes

``` r
## Importing Rds files
LOCO_df <- readRDS('../../LOCO_df.Rds')
LOCO_LFs_df <- readRDS('../../LOCO_LFs_df.Rds')
```

``` r
## Sampling the first 50 rows of each df
LOCO_df_sample <- LOCO_df[0:50,]
LOCO_LFs_df_sample <- LOCO_LFs_df[0:50,]
```

``` r
LOCO_df_sample
```

    ## # A tibble: 50 × 20
    ##    doc_id URL    website seeds date  subco…¹ title txt   txt_n…² txt_n…³ txt_n…⁴
    ##    <chr>  <chr>  <chr>   <chr> <chr> <chr>   <chr> <chr>   <dbl>   <dbl>   <dbl>
    ##  1 C00001 https… humans… mich… 2016… conspi… The … "For…    4075     160     120
    ##  2 C00003 https… americ… 5g; … 2017… conspi… ATTO… "LEA…     940      67      35
    ##  3 C00004 https… 911tru… sadd… 2009… conspi… What… "\"……     728      69      62
    ##  4 C00005 https… rense.… canc… <NA>  conspi… Canc… "The…    5055     207       1
    ##  5 C00007 https… awaren… clim… <NA>  conspi… Scie… "Whi…     403      13       5
    ##  6 C00008 https… geopol… moon… 2018… conspi… CIA … "The…     554      24      14
    ##  7 C00009 https… saveth… osam… <NA>  conspi… The … "If …    1198      85      21
    ##  8 C0000a https… whatre… cia.… <NA>  conspi… The … "Sid…     962      62      51
    ##  9 C0000b https… usawat… osam… <NA>  conspi… Trum… "Thi…     138       7       2
    ## 10 C0000c https… neonne… canc… 2018… conspi… Jour… "A b…     806      39      10
    ## # … with 40 more rows, 9 more variables: topic_k100 <chr>, topic_k200 <chr>,
    ## #   topic_k300 <chr>, mention_conspiracy <dbl>,
    ## #   conspiracy_representative <lgl>, cosine_similarity <dbl>, FB_shares <dbl>,
    ## #   FB_comments <dbl>, FB_reactions <dbl>, and abbreviated variable names
    ## #   ¹​subcorpus, ²​txt_nwords, ³​txt_nsentences, ⁴​txt_nparagraphs

``` r
LOCO_LFs_df_sample
```

    ## # A tibble: 50 × 288
    ##    doc_id LIWC_WC LIWC_Analytic LIWC_C…¹ LIWC_…² LIWC_…³ LIWC_…⁴ LIWC_…⁵ LIWC_…⁶
    ##    <chr>    <dbl>         <dbl>    <dbl>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ##  1 C00001    4051          88.9     77.0   14.0     5.22    22.3    27.2    79.3
    ##  2 C00003     929          99       68.2   10.4    37.2     14.3    31.1    61.0
    ##  3 C00004     706          90.0     53.4   29.1    25.8     30.7    26.9    75.1
    ##  4 C00005    4989          95.6     63.3   13.8    22.0     24.1    27.6    74.1
    ##  5 C00007     387          30.3     93.9    9.63   74.0     29.8    12.7    86.8
    ##  6 C00008     568          95.3     78.6   22.0     7.48    24.7    26.1    75.2
    ##  7 C00009    1166          84.3     67.2    8.32    9.08    13.0    22.7    78.9
    ##  8 C0000a     969          98.8     69.4   32.4    48.8     19.4    37.5    65.6
    ##  9 C0000b     136          91.0     52.9   14.7    25.8     19.4    17.6    87.5
    ## 10 C0000c     797          76.3     77.4   14.6    54.1     20.4    18.4    87.0
    ## # … with 40 more rows, 279 more variables: LIWC_function <dbl>,
    ## #   LIWC_pronoun <dbl>, LIWC_ppron <dbl>, LIWC_i <dbl>, LIWC_we <dbl>,
    ## #   LIWC_you <dbl>, LIWC_shehe <dbl>, LIWC_they <dbl>, LIWC_ipron <dbl>,
    ## #   LIWC_article <dbl>, LIWC_prep <dbl>, LIWC_auxverb <dbl>, LIWC_adverb <dbl>,
    ## #   LIWC_conj <dbl>, LIWC_negate <dbl>, LIWC_verb <dbl>, LIWC_adj <dbl>,
    ## #   LIWC_compare <dbl>, LIWC_interrog <dbl>, LIWC_number <dbl>,
    ## #   LIWC_quant <dbl>, LIWC_affect <dbl>, LIWC_posemo <dbl>, …

``` r
## Saving sample Rds files
write_rds(LOCO_df_sample, "LOCO_df_sample.Rds")
write_rds(LOCO_LFs_df_sample, "LOCO_LFs_df_sample.Rds")
```

## Data wrangling

By taking a look through the data objects above, I identified various
columns that are not useful for this particular analysis. In order to
shrink down the size of our dataframes, the code below will select only
the necessary columns.

``` r
LOCO_df_tidy <- LOCO_df %>% 
## Selecting columns with relevant metadata
  select(doc_id, date, website, subcorpus, title, txt_nwords, cosine_similarity, starts_with('FB_'))
## The size of the dataframe has been reduced considerably since removing the bulk of the data (such as raw text of each doc)
LOCO_df_tidy %>% 
  object.size() %>% 
  format("Mb")
```

    ## [1] "25.1 Mb"

``` r
LOCO_LFs_df_tidy <- LOCO_LFs_df %>% 
## Selecting only the LWIC columns that will be used for this analysis
  select(doc_id, starts_with('LIWC_'))
## The size of this dataframe has also been reduced, from 288 to 94 columns 
LOCO_LFs_df_tidy %>% 
  object.size() %>% 
  format("Mb")
```

    ## [1] "74.6 Mb"

## Joining the dataframes

``` r
## Joining the data by doc_id in order to conduct analyses
LOCO_final <- left_join(LOCO_df_tidy, LOCO_LFs_df_tidy)
```

    ## Joining, by = "doc_id"

``` r
## Saving final dataframe as Rds file
write_rds(LOCO_final, "LOCO_final.Rds")
```
