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
## Printing summary statistics about each column contained in the final dataframe
summary(LOCO_final)
```

    ##     doc_id              date             website           subcorpus        
    ##  Length:96743       Length:96743       Length:96743       Length:96743      
    ##  Class :character   Class :character   Class :character   Class :character  
    ##  Mode  :character   Mode  :character   Mode  :character   Mode  :character  
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##                                                                             
    ##     title             txt_nwords     cosine_similarity   FB_shares       
    ##  Length:96743       Min.   :  97.0   Min.   :0.00      Min.   :     0.0  
    ##  Class :character   1st Qu.: 343.0   1st Qu.:0.11      1st Qu.:     0.0  
    ##  Mode  :character   Median : 606.0   Median :0.14      Median :     3.0  
    ##                     Mean   : 912.4   Mean   :0.14      Mean   :   548.6  
    ##                     3rd Qu.:1037.0   3rd Qu.:0.17      3rd Qu.:    70.0  
    ##                     Max.   :9507.0   Max.   :0.28      Max.   :989192.0  
    ##                                      NA's   :72806     NA's   :8         
    ##   FB_comments       FB_reactions        LIWC_WC        LIWC_Analytic  
    ##  Min.   :      0   Min.   :      0   Min.   :   96.0   Min.   : 1.92  
    ##  1st Qu.:      0   1st Qu.:      0   1st Qu.:  335.0   1st Qu.:88.26  
    ##  Median :      0   Median :      1   Median :  592.0   Median :94.10  
    ##  Mean   :    683   Mean   :   2044   Mean   :  893.8   Mean   :90.62  
    ##  3rd Qu.:     37   3rd Qu.:    120   3rd Qu.: 1012.0   3rd Qu.:97.14  
    ##  Max.   :3257286   Max.   :5063897   Max.   :10421.0   Max.   :99.00  
    ##  NA's   :8         NA's   :8                                          
    ##    LIWC_Clout    LIWC_Authentic    LIWC_Tone        LIWC_WPS     
    ##  Min.   : 1.00   Min.   : 1.00   Min.   : 1.00   Min.   :  5.77  
    ##  1st Qu.:59.64   1st Qu.:12.91   1st Qu.:15.50   1st Qu.: 19.09  
    ##  Median :67.95   Median :21.60   Median :30.73   Median : 22.15  
    ##  Mean   :68.08   Mean   :25.66   Mean   :34.46   Mean   : 23.65  
    ##  3rd Qu.:76.63   3rd Qu.:34.45   3rd Qu.:49.52   3rd Qu.: 25.62  
    ##  Max.   :99.00   Max.   :99.00   Max.   :99.00   Max.   :701.00  
    ##                                                                  
    ##   LIWC_Sixltr       LIWC_Dic     LIWC_function    LIWC_pronoun   
    ##  Min.   : 3.87   Min.   :20.87   Min.   :11.76   Min.   : 0.000  
    ##  1st Qu.:22.34   1st Qu.:73.44   1st Qu.:40.84   1st Qu.: 5.260  
    ##  Median :25.60   Median :77.10   Median :43.89   Median : 7.080  
    ##  Mean   :25.69   Mean   :76.89   Mean   :43.99   Mean   : 7.489  
    ##  3rd Qu.:28.81   3rd Qu.:80.65   3rd Qu.:47.02   3rd Qu.: 9.230  
    ##  Max.   :56.29   Max.   :98.51   Max.   :68.81   Max.   :26.440  
    ##                                                                  
    ##    LIWC_ppron         LIWC_i           LIWC_we           LIWC_you      
    ##  Min.   : 0.000   Min.   : 0.0000   Min.   : 0.0000   Min.   : 0.0000  
    ##  1st Qu.: 1.590   1st Qu.: 0.0000   1st Qu.: 0.0000   1st Qu.: 0.0000  
    ##  Median : 2.860   Median : 0.0000   Median : 0.4100   Median : 0.1000  
    ##  Mean   : 3.408   Mean   : 0.4347   Mean   : 0.6508   Mean   : 0.4577  
    ##  3rd Qu.: 4.670   3rd Qu.: 0.4700   3rd Qu.: 0.9300   3rd Qu.: 0.4900  
    ##  Max.   :22.550   Max.   :14.6200   Max.   :10.9100   Max.   :16.1700  
    ##                                                                        
    ##    LIWC_shehe       LIWC_they        LIWC_ipron      LIWC_article   
    ##  Min.   : 0.000   Min.   :0.0000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 0.000   1st Qu.:0.2500   1st Qu.: 3.000   1st Qu.: 7.330  
    ##  Median : 0.530   Median :0.5900   Median : 4.000   Median : 8.520  
    ##  Mean   : 1.127   Mean   :0.7384   Mean   : 4.078   Mean   : 8.586  
    ##  3rd Qu.: 1.600   3rd Qu.:1.0400   3rd Qu.: 5.040   3rd Qu.: 9.770  
    ##  Max.   :14.710   Max.   :9.1200   Max.   :16.750   Max.   :23.040  
    ##                                                                     
    ##    LIWC_prep      LIWC_auxverb     LIWC_adverb       LIWC_conj    
    ##  Min.   : 0.00   Min.   : 0.000   Min.   : 0.000   Min.   : 0.00  
    ##  1st Qu.:13.65   1st Qu.: 5.010   1st Qu.: 2.160   1st Qu.: 4.31  
    ##  Median :14.75   Median : 6.270   Median : 2.980   Median : 5.17  
    ##  Mean   :14.77   Mean   : 6.339   Mean   : 3.069   Mean   : 5.23  
    ##  3rd Qu.:15.86   3rd Qu.: 7.590   3rd Qu.: 3.870   3rd Qu.: 6.06  
    ##  Max.   :28.85   Max.   :18.050   Max.   :14.930   Max.   :16.30  
    ##                                                                   
    ##   LIWC_negate        LIWC_verb        LIWC_adj       LIWC_compare  
    ##  Min.   : 0.0000   Min.   : 0.00   Min.   : 0.000   Min.   : 0.00  
    ##  1st Qu.: 0.4300   1st Qu.: 9.27   1st Qu.: 3.640   1st Qu.: 1.79  
    ##  Median : 0.7900   Median :11.24   Median : 4.460   Median : 2.38  
    ##  Mean   : 0.8824   Mean   :11.36   Mean   : 4.568   Mean   : 2.47  
    ##  3rd Qu.: 1.2200   3rd Qu.:13.28   3rd Qu.: 5.370   3rd Qu.: 3.03  
    ##  Max.   :18.4400   Max.   :29.61   Max.   :18.750   Max.   :13.72  
    ##                                                                    
    ##  LIWC_interrog    LIWC_number       LIWC_quant      LIWC_affect    
    ##  Min.   :0.000   Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.:0.770   1st Qu.: 1.540   1st Qu.: 1.350   1st Qu.: 2.740  
    ##  Median :1.170   Median : 2.460   Median : 1.880   Median : 3.730  
    ##  Mean   :1.239   Mean   : 2.908   Mean   : 1.975   Mean   : 3.872  
    ##  3rd Qu.:1.630   3rd Qu.: 3.740   3rd Qu.: 2.480   3rd Qu.: 4.810  
    ##  Max.   :8.890   Max.   :24.020   Max.   :16.280   Max.   :19.050  
    ##                                                                    
    ##   LIWC_posemo      LIWC_negemo        LIWC_anx         LIWC_anger    
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.0000   Min.   :0.0000  
    ##  1st Qu.: 1.260   1st Qu.: 0.880   1st Qu.: 0.0000   1st Qu.:0.0000  
    ##  Median : 1.890   Median : 1.570   Median : 0.2500   Median :0.3700  
    ##  Mean   : 2.042   Mean   : 1.775   Mean   : 0.3692   Mean   :0.5884  
    ##  3rd Qu.: 2.620   3rd Qu.: 2.410   3rd Qu.: 0.5200   3rd Qu.:0.8300  
    ##  Max.   :15.160   Max.   :17.190   Max.   :11.7600   Max.   :7.6900  
    ##                                                                      
    ##     LIWC_sad       LIWC_social      LIWC_family      LIWC_friend    
    ##  Min.   :0.0000   Min.   : 0.000   Min.   :0.0000   Min.   :0.0000  
    ##  1st Qu.:0.0000   1st Qu.: 5.160   1st Qu.:0.0000   1st Qu.:0.0000  
    ##  Median :0.2000   Median : 7.180   Median :0.0000   Median :0.0000  
    ##  Mean   :0.2887   Mean   : 7.589   Mean   :0.3083   Mean   :0.1583  
    ##  3rd Qu.:0.4200   3rd Qu.: 9.560   3rd Qu.:0.3300   3rd Qu.:0.2300  
    ##  Max.   :7.6600   Max.   :30.150   Max.   :8.9900   Max.   :6.5200  
    ##                                                                     
    ##   LIWC_female        LIWC_male       LIWC_cogproc     LIWC_insight   
    ##  Min.   : 0.0000   Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 0.0000   1st Qu.: 0.000   1st Qu.: 6.990   1st Qu.: 1.210  
    ##  Median : 0.0000   Median : 0.500   Median : 8.840   Median : 1.790  
    ##  Mean   : 0.4469   Mean   : 1.094   Mean   : 9.001   Mean   : 1.943  
    ##  3rd Qu.: 0.3900   3rd Qu.: 1.510   3rd Qu.:10.800   3rd Qu.: 2.490  
    ##  Max.   :15.4400   Max.   :18.000   Max.   :33.850   Max.   :15.320  
    ##                                                                      
    ##    LIWC_cause      LIWC_discrep      LIWC_tentat      LIWC_certain  
    ##  Min.   : 0.000   Min.   : 0.0000   Min.   : 0.000   Min.   : 0.00  
    ##  1st Qu.: 1.200   1st Qu.: 0.4700   1st Qu.: 1.140   1st Qu.: 0.54  
    ##  Median : 1.780   Median : 0.8600   Median : 1.760   Median : 0.90  
    ##  Mean   : 1.936   Mean   : 0.9768   Mean   : 1.958   Mean   : 0.99  
    ##  3rd Qu.: 2.500   3rd Qu.: 1.3500   3rd Qu.: 2.510   3rd Qu.: 1.34  
    ##  Max.   :13.390   Max.   :12.5000   Max.   :15.740   Max.   :10.00  
    ##                                                                     
    ##   LIWC_differ      LIWC_percept       LIWC_see         LIWC_hear      
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.0000   Min.   : 0.0000  
    ##  1st Qu.: 1.500   1st Qu.: 1.170   1st Qu.: 0.3100   1st Qu.: 0.2000  
    ##  Median : 2.170   Median : 1.880   Median : 0.6200   Median : 0.6200  
    ##  Mean   : 2.274   Mean   : 2.112   Mean   : 0.8218   Mean   : 0.8235  
    ##  3rd Qu.: 2.910   3rd Qu.: 2.770   3rd Qu.: 1.0600   3rd Qu.: 1.2300  
    ##  Max.   :15.360   Max.   :15.730   Max.   :13.0700   Max.   :12.8800  
    ##                                                                       
    ##    LIWC_feel          LIWC_bio        LIWC_body        LIWC_health    
    ##  Min.   : 0.0000   Min.   : 0.000   Min.   : 0.0000   Min.   : 0.000  
    ##  1st Qu.: 0.0000   1st Qu.: 0.770   1st Qu.: 0.0000   1st Qu.: 0.260  
    ##  Median : 0.2100   Median : 1.670   Median : 0.2600   Median : 0.760  
    ##  Mean   : 0.3419   Mean   : 2.668   Mean   : 0.5493   Mean   : 1.725  
    ##  3rd Qu.: 0.4500   3rd Qu.: 3.690   3rd Qu.: 0.6200   3rd Qu.: 2.500  
    ##  Max.   :12.5500   Max.   :25.230   Max.   :19.8600   Max.   :23.420  
    ##                                                                       
    ##   LIWC_sexual       LIWC_ingest       LIWC_drives     LIWC_affiliation
    ##  Min.   : 0.0000   Min.   : 0.0000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 0.0000   1st Qu.: 0.0000   1st Qu.: 5.970   1st Qu.: 0.870  
    ##  Median : 0.0000   Median : 0.0800   Median : 7.570   Median : 1.480  
    ##  Mean   : 0.2094   Mean   : 0.3553   Mean   : 7.757   Mean   : 1.723  
    ##  3rd Qu.: 0.0800   3rd Qu.: 0.3200   3rd Qu.: 9.350   3rd Qu.: 2.300  
    ##  Max.   :14.4100   Max.   :23.0300   Max.   :23.600   Max.   :13.740  
    ##                                                                       
    ##   LIWC_achieve     LIWC_power      LIWC_reward        LIWC_risk    
    ##  Min.   : 0.00   Min.   : 0.000   Min.   : 0.0000   Min.   :0.000  
    ##  1st Qu.: 0.93   1st Qu.: 2.390   1st Qu.: 0.4600   1st Qu.:0.280  
    ##  Median : 1.41   Median : 3.420   Median : 0.7800   Median :0.620  
    ##  Mean   : 1.56   Mean   : 3.677   Mean   : 0.8801   Mean   :0.751  
    ##  3rd Qu.: 2.00   3rd Qu.: 4.700   3rd Qu.: 1.1800   3rd Qu.:1.060  
    ##  Max.   :13.27   Max.   :20.560   Max.   :13.8100   Max.   :9.490  
    ##                                                                    
    ##  LIWC_focuspast  LIWC_focuspresent LIWC_focusfuture  LIWC_relativ  
    ##  Min.   : 0.00   Min.   : 0.000    Min.   : 0.00    Min.   : 0.00  
    ##  1st Qu.: 2.21   1st Qu.: 4.620    1st Qu.: 0.46    1st Qu.:11.75  
    ##  Median : 3.45   Median : 6.340    Median : 0.81    Median :13.69  
    ##  Mean   : 3.78   Mean   : 6.498    Mean   : 0.96    Mean   :13.96  
    ##  3rd Qu.: 5.03   3rd Qu.: 8.180    3rd Qu.: 1.30    3rd Qu.:15.91  
    ##  Max.   :17.16   Max.   :25.000    Max.   :10.23    Max.   :34.16  
    ##                                                                    
    ##   LIWC_motion       LIWC_space       LIWC_time        LIWC_work     
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 1.080   1st Qu.: 6.240   1st Qu.: 3.420   1st Qu.: 2.520  
    ##  Median : 1.560   Median : 7.470   Median : 4.460   Median : 3.850  
    ##  Mean   : 1.686   Mean   : 7.682   Mean   : 4.677   Mean   : 4.265  
    ##  3rd Qu.: 2.130   3rd Qu.: 8.890   3rd Qu.: 5.710   3rd Qu.: 5.510  
    ##  Max.   :12.400   Max.   :30.480   Max.   :21.600   Max.   :27.340  
    ##                                                                     
    ##   LIWC_leisure       LIWC_home        LIWC_money       LIWC_relig     
    ##  Min.   : 0.0000   Min.   :0.0000   Min.   : 0.000   Min.   : 0.0000  
    ##  1st Qu.: 0.2400   1st Qu.:0.0000   1st Qu.: 0.210   1st Qu.: 0.0000  
    ##  Median : 0.5700   Median :0.1700   Median : 0.570   Median : 0.0000  
    ##  Mean   : 0.9166   Mean   :0.3357   Mean   : 1.053   Mean   : 0.2982  
    ##  3rd Qu.: 1.1500   3rd Qu.:0.4500   3rd Qu.: 1.280   3rd Qu.: 0.2800  
    ##  Max.   :14.6900   Max.   :9.5700   Max.   :19.020   Max.   :14.7000  
    ##                                                                       
    ##    LIWC_death      LIWC_informal       LIWC_swear      LIWC_netspeak    
    ##  Min.   : 0.0000   Min.   : 0.0000   Min.   :0.00000   Min.   : 0.0000  
    ##  1st Qu.: 0.0000   1st Qu.: 0.0000   1st Qu.:0.00000   1st Qu.: 0.0000  
    ##  Median : 0.1700   Median : 0.1700   Median :0.00000   Median : 0.0000  
    ##  Mean   : 0.4308   Mean   : 0.2694   Mean   :0.02158   Mean   : 0.1216  
    ##  3rd Qu.: 0.5700   3rd Qu.: 0.3700   3rd Qu.:0.00000   3rd Qu.: 0.1100  
    ##  Max.   :15.0000   Max.   :11.1700   Max.   :3.20000   Max.   :10.9100  
    ##                                                                         
    ##   LIWC_assent        LIWC_nonflu      LIWC_filler        LIWC_AllPunc   
    ##  Min.   : 0.00000   Min.   :0.0000   Min.   :0.000000   Min.   :  3.19  
    ##  1st Qu.: 0.00000   1st Qu.:0.0000   1st Qu.:0.000000   1st Qu.: 14.29  
    ##  Median : 0.00000   Median :0.0000   Median :0.000000   Median : 16.53  
    ##  Mean   : 0.04786   Mean   :0.0804   Mean   :0.003146   Mean   : 16.94  
    ##  3rd Qu.: 0.00000   3rd Qu.:0.1200   3rd Qu.:0.000000   3rd Qu.: 19.08  
    ##  Max.   :11.17000   Max.   :4.7200   Max.   :3.510000   Max.   :161.86  
    ##                                                                         
    ##   LIWC_Period       LIWC_Comma       LIWC_Colon        LIWC_SemiC      
    ##  Min.   : 0.000   Min.   : 0.000   Min.   : 0.0000   Min.   : 0.00000  
    ##  1st Qu.: 4.100   1st Qu.: 4.260   1st Qu.: 0.0000   1st Qu.: 0.00000  
    ##  Median : 4.770   Median : 5.300   Median : 0.1900   Median : 0.00000  
    ##  Mean   : 4.957   Mean   : 5.363   Mean   : 0.3304   Mean   : 0.08726  
    ##  3rd Qu.: 5.600   3rd Qu.: 6.350   3rd Qu.: 0.4600   3rd Qu.: 0.07000  
    ##  Max.   :65.430   Max.   :31.580   Max.   :13.3200   Max.   :67.45000  
    ##                                                                        
    ##    LIWC_QMark       LIWC_Exclam        LIWC_Dash        LIWC_Quote    
    ##  Min.   : 0.0000   Min.   :0.00000   Min.   : 0.000   Min.   : 0.000  
    ##  1st Qu.: 0.0000   1st Qu.:0.00000   1st Qu.: 0.570   1st Qu.: 0.520  
    ##  Median : 0.0000   Median :0.00000   Median : 1.000   Median : 1.600  
    ##  Mean   : 0.1606   Mean   :0.05074   Mean   : 1.206   Mean   : 1.883  
    ##  3rd Qu.: 0.2000   3rd Qu.:0.00000   3rd Qu.: 1.600   3rd Qu.: 2.840  
    ##  Max.   :12.7800   Max.   :8.64000   Max.   :41.210   Max.   :27.220  
    ##                                                                       
    ##   LIWC_Apostro     LIWC_Parenth      LIWC_OtherP      
    ##  Min.   : 0.000   Min.   : 0.0000   Min.   :  0.0000  
    ##  1st Qu.: 0.700   1st Qu.: 0.0000   1st Qu.:  0.0000  
    ##  Median : 1.300   Median : 0.3800   Median :  0.2600  
    ##  Mean   : 1.494   Mean   : 0.7222   Mean   :  0.6889  
    ##  3rd Qu.: 2.030   3rd Qu.: 0.9900   3rd Qu.:  0.7700  
    ##  Max.   :25.740   Max.   :32.3800   Max.   :144.4900  
    ## 

``` r
## Saving final dataframe as Rds file
write_rds(LOCO_final, "LOCO_final.Rds")
```
