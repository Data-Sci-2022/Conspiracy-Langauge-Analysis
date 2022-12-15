Data analysis walkthrough
================
Sen Sub Laban
December 15, 2022

-   <a href="#data-import" id="toc-data-import">Data import</a>
-   <a href="#visualizing-fb-engagement-variables"
    id="toc-visualizing-fb-engagement-variables">Visualizing FB engagement
    variables</a>
-   <a href="#creating-subsets-of-data-based-on-subcorpora"
    id="toc-creating-subsets-of-data-based-on-subcorpora">Creating subsets
    of data based on subcorpora</a>
-   <a href="#generating-random-forests"
    id="toc-generating-random-forests">Generating random forests</a>
-   <a href="#creating-variable-importance-tibble"
    id="toc-creating-variable-importance-tibble">Creating variable
    importance tibble</a>
-   <a href="#visualizing-variable-importance-rankings"
    id="toc-visualizing-variable-importance-rankings">Visualizing variable
    importance rankings</a>
-   <a href="#variable-importance-comparison-scatterplot"
    id="toc-variable-importance-comparison-scatterplot">Variable importance
    comparison scatterplot</a>
-   <a href="#creating-linear-models"
    id="toc-creating-linear-models">Creating linear models</a>

## Data import

``` r
## Reading in the finalized data frame
loco <- readRDS('LOCO_final.Rds')
```

``` r
## Preview of the data, first 6 rows 
head(loco)
```

    ## # A tibble: 6 × 103
    ##   doc_id date      website subco…¹ title txt_n…² cosin…³ FB_sh…⁴ FB_co…⁵ FB_re…⁶
    ##   <chr>  <chr>     <chr>   <chr>   <chr>   <dbl>   <dbl>   <dbl>   <dbl>   <dbl>
    ## 1 C00001 2016-12-… humans… conspi… The …    4075  0.177       13       2       4
    ## 2 C00003 2017-08-… americ… conspi… ATTO…     940  0.0878       0       0       0
    ## 3 C00004 2009-04-… 911tru… conspi… What…     728  0.136        0       0       0
    ## 4 C00005 <NA>      rense.… conspi… Canc…    5055  0.149        3       0       0
    ## 5 C00007 <NA>      awaren… conspi… Scie…     403  0.140       26       4      55
    ## 6 C00008 2018-02-… geopol… conspi… CIA …     554  0.163        0       0       0
    ## # … with 93 more variables: LIWC_WC <dbl>, LIWC_Analytic <dbl>,
    ## #   LIWC_Clout <dbl>, LIWC_Authentic <dbl>, LIWC_Tone <dbl>, LIWC_WPS <dbl>,
    ## #   LIWC_Sixltr <dbl>, LIWC_Dic <dbl>, LIWC_function <dbl>, LIWC_pronoun <dbl>,
    ## #   LIWC_ppron <dbl>, LIWC_i <dbl>, LIWC_we <dbl>, LIWC_you <dbl>,
    ## #   LIWC_shehe <dbl>, LIWC_they <dbl>, LIWC_ipron <dbl>, LIWC_article <dbl>,
    ## #   LIWC_prep <dbl>, LIWC_auxverb <dbl>, LIWC_adverb <dbl>, LIWC_conj <dbl>,
    ## #   LIWC_negate <dbl>, LIWC_verb <dbl>, LIWC_adj <dbl>, LIWC_compare <dbl>, …

## Visualizing FB engagement variables

``` r
## Conspiracy Facebook engagement
options(scipen=999)
loco %>% 
  filter(subcorpus=="conspiracy") %>% 
  pivot_longer(starts_with("FB_"), names_to="type", values_to="number") %>%
  filter(number > 0) %>% 
  group_by(type) %>% 
  mutate(mean_engage=mean(number), skewness=skewness(number)) %>% 
  ggplot(aes(x=number)) + 
  geom_density(color="black", fill="gray") +
  scale_x_log10() +
  facet_grid(type ~ .) +
  geom_text(aes(x=100000, y=0.1, label=round(skewness,2), color="red")) +
  ggtitle("Conspiracy FB engagement variables") +
  guides(colour=FALSE) +
  theme_bw()
```

    ## Warning: `guides(<scale> = FALSE)` is deprecated. Please use `guides(<scale> =
    ## "none")` instead.

![](images/Conspiracy%20Facebook%20engagements-1.png)<!-- -->

``` r
## Mainstream Facebook engagement
options(scipen=999)
loco %>% 
  filter(!subcorpus=="conspiracy") %>% 
  pivot_longer(starts_with("FB_"), names_to="type", values_to="number") %>%
  filter(number > 0) %>%
  group_by(type) %>% 
  mutate(mean_engage=mean(number), skewness=skewness(number)) %>% 
  ggplot(aes(x=number)) + 
  geom_density(color="black", fill="gray") +
  scale_x_log10() +
  facet_grid(type ~ .) +
  geom_text(aes(x=100000, y=0.1, label=round(skewness,2), color="red")) +
  ggtitle("Mainstream FB engagement variables") +
  guides(colour=FALSE) +
  theme_bw()
```

    ## Warning: `guides(<scale> = FALSE)` is deprecated. Please use `guides(<scale> =
    ## "none")` instead.

![](images/Mainstream%20Facebook%20engagements-1.png)<!-- -->

## Creating subsets of data based on subcorpora

``` r
## Creating a subset of conspiracy documents only
consp_engage <- loco %>% 
  filter(subcorpus == "conspiracy") %>% 
  ## Creating new column engagement that sums shares, reactions, and comments
  mutate(engagement = rowSums(.[8:10])) %>% 
  relocate(engagement, .after= FB_reactions) %>% 
  ## Filtering documents with no engagement at all
  filter(engagement > 0) %>% 
  mutate(engagelog = log(engagement)) %>% 
  relocate(engagelog, .after= engagement)
```

``` r
## Creating a subset of mainstream documents only
mainst_engage <- loco %>% 
  filter(!subcorpus == "conspiracy") %>% 
  mutate(engagement = rowSums(.[8:10])) %>% 
  relocate(engagement, .after= FB_reactions) %>% 
  filter(engagement > 0) %>% 
  mutate(engagelog = log(engagement)) %>% 
  relocate(engagelog, .after=engagement)
```

``` r
## Visualizing new overall engagement variable
consp_engage %>% 
  mutate(mean_engage=mean(engagement), skewness=skewness(engagement)) %>% 
  ggplot(aes(x=engagement)) +
  geom_density(color="black", fill="gray")+
  scale_x_log10() +
  geom_text(aes(x=100000, y=0.15, label=round(mean_engage,2))) +
  geom_text(aes(x=100000, y=0.1, label=round(skewness,2), color="red")) +
  ggtitle("Conspiracy overall engagement") +
  guides(colour=FALSE) +
  theme_bw()
```

    ## Warning: `guides(<scale> = FALSE)` is deprecated. Please use `guides(<scale> =
    ## "none")` instead.

![](images/Conspiracy%20overall%20engagement-1.png)<!-- -->

``` r
mainst_engage %>% 
  mutate(mean_engage=mean(engagement), skewness=skewness(engagement)) %>% 
  ggplot(aes(x=engagement)) +
  geom_density(color="black", fill="gray")+
  scale_x_log10() +
  geom_text(aes(x=100000, y=0.15, label=round(mean_engage,2))) +
  geom_text(aes(x=100000, y=0.1, label=round(skewness,2), color="red")) +
  ggtitle("Mainstream overall engagement") +
  guides(colour=FALSE) +
  theme_bw()
```

    ## Warning: `guides(<scale> = FALSE)` is deprecated. Please use `guides(<scale> =
    ## "none")` instead.

![](images/Mainstream%20overall%20engagement-1.png)<!-- -->

## Generating random forests

``` r
## Generating random forest for conspiracy documents
set.seed(12)
consp_forest <- ranger(engagelog ~ LIWC_WC + LIWC_Analytic + LIWC_Clout + LIWC_Authentic + LIWC_Tone + LIWC_WPS + LIWC_Sixltr + LIWC_Dic + LIWC_function + LIWC_pronoun + LIWC_ppron + LIWC_i + LIWC_we + LIWC_you + LIWC_shehe + LIWC_they + LIWC_ipron + LIWC_article + LIWC_prep + LIWC_auxverb + LIWC_adverb + LIWC_conj + LIWC_negate +   LIWC_verb + LIWC_adj + LIWC_compare + LIWC_interrog + LIWC_number + LIWC_quant + LIWC_affect + LIWC_posemo + LIWC_negemo + LIWC_anx + LIWC_anger + LIWC_sad + LIWC_social + LIWC_family + LIWC_friend + LIWC_female + LIWC_male + LIWC_cogproc + LIWC_insight + LIWC_cause + LIWC_discrep + LIWC_tentat + LIWC_certain + LIWC_differ + LIWC_percept + LIWC_see + LIWC_hear + LIWC_feel + LIWC_bio + LIWC_body + LIWC_health + LIWC_sexual + LIWC_ingest + LIWC_drives + LIWC_affiliation + LIWC_achieve + LIWC_power + LIWC_reward + LIWC_risk + LIWC_focuspast + LIWC_focuspresent + LIWC_focusfuture + LIWC_relativ + LIWC_motion + LIWC_space + LIWC_time + LIWC_work + LIWC_leisure + LIWC_home + LIWC_money + LIWC_relig + LIWC_death + LIWC_informal + LIWC_swear + LIWC_netspeak + LIWC_assent + LIWC_nonflu + LIWC_filler + LIWC_AllPunc + LIWC_Period + LIWC_Comma + LIWC_Colon + LIWC_SemiC + LIWC_QMark + LIWC_Exclam + LIWC_Dash + LIWC_Quote + LIWC_Apostro + LIWC_Parenth + LIWC_OtherP, 
                   data=consp_engage, 
                   importance='impurity', mtry=3)
```

``` r
## Generating random forest for mainstream documents
set.seed(12)
mainst_forest <- ranger(engagelog ~ LIWC_WC + LIWC_Analytic + LIWC_Clout + LIWC_Authentic + LIWC_Tone + LIWC_WPS + LIWC_Sixltr + LIWC_Dic + LIWC_function + LIWC_pronoun + LIWC_ppron + LIWC_i + LIWC_we + LIWC_you + LIWC_shehe + LIWC_they + LIWC_ipron + LIWC_article + LIWC_prep + LIWC_auxverb + LIWC_adverb + LIWC_conj + LIWC_negate +   LIWC_verb + LIWC_adj + LIWC_compare + LIWC_interrog + LIWC_number + LIWC_quant + LIWC_affect + LIWC_posemo + LIWC_negemo + LIWC_anx + LIWC_anger + LIWC_sad + LIWC_social + LIWC_family + LIWC_friend + LIWC_female + LIWC_male + LIWC_cogproc + LIWC_insight + LIWC_cause + LIWC_discrep + LIWC_tentat + LIWC_certain + LIWC_differ + LIWC_percept + LIWC_see + LIWC_hear + LIWC_feel + LIWC_bio + LIWC_body + LIWC_health + LIWC_sexual + LIWC_ingest + LIWC_drives + LIWC_affiliation + LIWC_achieve + LIWC_power + LIWC_reward + LIWC_risk + LIWC_focuspast + LIWC_focuspresent + LIWC_focusfuture + LIWC_relativ + LIWC_motion + LIWC_space + LIWC_time + LIWC_work + LIWC_leisure + LIWC_home + LIWC_money + LIWC_relig + LIWC_death + LIWC_informal + LIWC_swear + LIWC_netspeak + LIWC_assent + LIWC_nonflu + LIWC_filler + LIWC_AllPunc + LIWC_Period + LIWC_Comma + LIWC_Colon + LIWC_SemiC + LIWC_QMark + LIWC_Exclam + LIWC_Dash + LIWC_Quote + LIWC_Apostro + LIWC_Parenth + LIWC_OtherP, 
                   data=mainst_engage, 
                   importance='impurity', mtry=3)
```

``` r
consp_forest
```

    ## Ranger result
    ## 
    ## Call:
    ##  ranger(engagelog ~ LIWC_WC + LIWC_Analytic + LIWC_Clout + LIWC_Authentic +      LIWC_Tone + LIWC_WPS + LIWC_Sixltr + LIWC_Dic + LIWC_function +      LIWC_pronoun + LIWC_ppron + LIWC_i + LIWC_we + LIWC_you +      LIWC_shehe + LIWC_they + LIWC_ipron + LIWC_article + LIWC_prep +      LIWC_auxverb + LIWC_adverb + LIWC_conj + LIWC_negate + LIWC_verb +      LIWC_adj + LIWC_compare + LIWC_interrog + LIWC_number + LIWC_quant +      LIWC_affect + LIWC_posemo + LIWC_negemo + LIWC_anx + LIWC_anger +      LIWC_sad + LIWC_social + LIWC_family + LIWC_friend + LIWC_female +      LIWC_male + LIWC_cogproc + LIWC_insight + LIWC_cause + LIWC_discrep +      LIWC_tentat + LIWC_certain + LIWC_differ + LIWC_percept +      LIWC_see + LIWC_hear + LIWC_feel + LIWC_bio + LIWC_body +      LIWC_health + LIWC_sexual + LIWC_ingest + LIWC_drives + LIWC_affiliation +      LIWC_achieve + LIWC_power + LIWC_reward + LIWC_risk + LIWC_focuspast +      LIWC_focuspresent + LIWC_focusfuture + LIWC_relativ + LIWC_motion +      LIWC_space + LIWC_time + LIWC_work + LIWC_leisure + LIWC_home +      LIWC_money + LIWC_relig + LIWC_death + LIWC_informal + LIWC_swear +      LIWC_netspeak + LIWC_assent + LIWC_nonflu + LIWC_filler +      LIWC_AllPunc + LIWC_Period + LIWC_Comma + LIWC_Colon + LIWC_SemiC +      LIWC_QMark + LIWC_Exclam + LIWC_Dash + LIWC_Quote + LIWC_Apostro +      LIWC_Parenth + LIWC_OtherP, data = consp_engage, importance = "impurity",      mtry = 3) 
    ## 
    ## Type:                             Regression 
    ## Number of trees:                  500 
    ## Sample size:                      13113 
    ## Number of independent variables:  93 
    ## Mtry:                             3 
    ## Target node size:                 5 
    ## Variable importance mode:         impurity 
    ## Splitrule:                        variance 
    ## OOB prediction error (MSE):       5.73591 
    ## R squared (OOB):                  0.08081433

``` r
mainst_forest
```

    ## Ranger result
    ## 
    ## Call:
    ##  ranger(engagelog ~ LIWC_WC + LIWC_Analytic + LIWC_Clout + LIWC_Authentic +      LIWC_Tone + LIWC_WPS + LIWC_Sixltr + LIWC_Dic + LIWC_function +      LIWC_pronoun + LIWC_ppron + LIWC_i + LIWC_we + LIWC_you +      LIWC_shehe + LIWC_they + LIWC_ipron + LIWC_article + LIWC_prep +      LIWC_auxverb + LIWC_adverb + LIWC_conj + LIWC_negate + LIWC_verb +      LIWC_adj + LIWC_compare + LIWC_interrog + LIWC_number + LIWC_quant +      LIWC_affect + LIWC_posemo + LIWC_negemo + LIWC_anx + LIWC_anger +      LIWC_sad + LIWC_social + LIWC_family + LIWC_friend + LIWC_female +      LIWC_male + LIWC_cogproc + LIWC_insight + LIWC_cause + LIWC_discrep +      LIWC_tentat + LIWC_certain + LIWC_differ + LIWC_percept +      LIWC_see + LIWC_hear + LIWC_feel + LIWC_bio + LIWC_body +      LIWC_health + LIWC_sexual + LIWC_ingest + LIWC_drives + LIWC_affiliation +      LIWC_achieve + LIWC_power + LIWC_reward + LIWC_risk + LIWC_focuspast +      LIWC_focuspresent + LIWC_focusfuture + LIWC_relativ + LIWC_motion +      LIWC_space + LIWC_time + LIWC_work + LIWC_leisure + LIWC_home +      LIWC_money + LIWC_relig + LIWC_death + LIWC_informal + LIWC_swear +      LIWC_netspeak + LIWC_assent + LIWC_nonflu + LIWC_filler +      LIWC_AllPunc + LIWC_Period + LIWC_Comma + LIWC_Colon + LIWC_SemiC +      LIWC_QMark + LIWC_Exclam + LIWC_Dash + LIWC_Quote + LIWC_Apostro +      LIWC_Parenth + LIWC_OtherP, data = mainst_engage, importance = "impurity",      mtry = 3) 
    ## 
    ## Type:                             Regression 
    ## Number of trees:                  500 
    ## Sample size:                      45714 
    ## Number of independent variables:  93 
    ## Mtry:                             3 
    ## Target node size:                 5 
    ## Variable importance mode:         impurity 
    ## Splitrule:                        variance 
    ## OOB prediction error (MSE):       7.310037 
    ## R squared (OOB):                  0.09382468

## Creating variable importance tibble

``` r
## Creating a tibble of conspiracy vi
consp_vi <- vi(consp_forest) %>% 
  separate(Variable, c("del", "Variable"), sep="_") %>% 
  select(-del) %>% 
  mutate(Rank=row_number()) %>% 
  relocate(Rank, .before=Variable)
```

``` r
## Creating a tibble of mainstream vi
mainst_vi <- vi(mainst_forest) %>% 
  separate(Variable, c("del", "Variable"), sep="_") %>% 
  select(-del) %>% 
  mutate(Rank=row_number()) %>% 
  relocate(Rank, .before=Variable)
```

``` r
consp_vi[0:10,]
```

    ## # A tibble: 10 × 3
    ##     Rank Variable Importance
    ##    <int> <chr>         <dbl>
    ##  1     1 Clout         1047.
    ##  2     2 social        1044.
    ##  3     3 Period        1003.
    ##  4     4 Quote          996.
    ##  5     5 WC             985.
    ##  6     6 ipron          981.
    ##  7     7 WPS            968.
    ##  8     8 time           959.
    ##  9     9 article        955.
    ## 10    10 relativ        938.

``` r
mainst_vi[0:10,]
```

    ## # A tibble: 10 × 3
    ##     Rank Variable  Importance
    ##    <int> <chr>          <dbl>
    ##  1     1 Quote          5324.
    ##  2     2 Comma          5048.
    ##  3     3 hear           4859.
    ##  4     4 WC             4836.
    ##  5     5 verb           4771.
    ##  6     6 focuspast      4725.
    ##  7     7 Dic            4686.
    ##  8     8 function       4677.
    ##  9     9 Apostro        4616.
    ## 10    10 AllPunc        4591.

## Visualizing variable importance rankings

``` r
## Creating barplots of conspiracy variable importance ranking
consp_plots <- consp_vi %>% 
  split(factor(sort(rank(row.names(consp_vi))%%3))) %>% 
  map(~ggplot(.x, aes(x=reorder(Variable, Importance), y=Importance)) +
        geom_bar(stat='identity') +
        coord_flip() +
        xlab("Variable") +
        ylim(0,1050) +
        ggtitle("Conspiracy variable importance") +
        theme_bw())
```

``` r
consp_plots
```

    ## $`0`

![](images/Conspiracy%20VI%20plots-1.png)<!-- -->

    ## 
    ## $`1`

![](images/Conspiracy%20VI%20plots-2.png)<!-- -->

    ## 
    ## $`2`

![](images/Conspiracy%20VI%20plots-3.png)<!-- -->

``` r
## Creating barplots of mainstream variable importance ranking
mainst_plots <- mainst_vi %>% 
  split(factor(sort(rank(row.names(consp_vi))%%3))) %>% 
  map(~ggplot(.x, aes(x=reorder(Variable, Importance), y=Importance)) +
        geom_bar(stat='identity') +
        coord_flip() +
        xlab("Variable") +
        ylim(0,5500) +
        ggtitle("Mainstream variable importance") +
        theme_bw())
```

``` r
mainst_plots
```

    ## $`0`

![](images/Mainstream%20VI%20plots-1.png)<!-- -->

    ## 
    ## $`1`

![](images/Mainstream%20VI%20plots-2.png)<!-- -->

    ## 
    ## $`2`

![](images/Mainstream%20VI%20plots-3.png)<!-- -->

## Variable importance comparison scatterplot

``` r
## Joining variable importance tibbles for comparison 
vi_join <- left_join(consp_vi, mainst_vi, by="Variable") %>% 
  setNames(c("C_Rank", "Variable", "C_Importance", "M_Rank", "M_Importance")) %>% 
  select(Variable, everything())
```

``` r
## Creating scatter plot comparing overall vi ranking
vi_join %>% 
  ggplot(aes(x=C_Rank, y=M_Rank, label=Variable)) +
  labs(x= "Conspiracy Feature Rank", y="Mainstream Feature Rank") +
  ggtitle("Feature rank comparison") +
  ## Reversing scales so better ranks (lower number) appear higher
  scale_y_reverse() +
  scale_x_reverse() +
  geom_text(alpha=0.7)
```

![](images/Overall%20VI%20Rank-1.png)<!-- -->

``` r
## Creating a scatter plot of the top 1/3 conspiracy variables
vi_join %>% 
  filter(C_Rank <= 31) %>% 
  ggplot(aes(x=C_Rank, y=M_Rank, label=Variable)) +
  labs(x= "Conspiracy Feature Rank", y="Mainstream Feature Rank") +
  ggtitle("Feature rank comparison (Top Conspiracy VIs)") +
  scale_y_reverse() +
  scale_x_reverse() +
  geom_text(alpha=0.7)
```

![](images/Top%20Conspiracy%20VI%20plot-1.png)<!-- -->

``` r
## Creating a scatter plot of the top 1/3 mainstream variables
vi_join %>% 
  filter(M_Rank <= 31) %>% 
  ggplot(aes(x=C_Rank, y=M_Rank, label=Variable)) +
  labs(x= "Conspiracy Feature Rank", y="Mainstream Feature Rank") +
  ggtitle("Feature rank comparison (Top Mainstream VIs)") +
  scale_y_reverse() +
  scale_x_reverse() +
  geom_text(alpha=0.7)
```

![](images/Top%20Mainstream%20VI%20plot-1.png)<!-- -->

## Creating linear models

``` r
consp_lm <- lm(engagelog ~ LIWC_Clout + LIWC_social + LIWC_ipron + LIWC_time + LIWC_relativ + LIWC_interrog +
                LIWC_affect + LIWC_affiliation + LIWC_drives + LIWC_focuspresent + LIWC_insight + 
                LIWC_focuspast + LIWC_percept + LIWC_negemo + LIWC_space + LIWC_ppron + LIWC_power +
                LIWC_cause + LIWC_tentat + LIWC_hear, consp_engage)
```

``` r
summary(consp_lm)
```

    ## 
    ## Call:
    ## lm(formula = engagelog ~ LIWC_Clout + LIWC_social + LIWC_ipron + 
    ##     LIWC_time + LIWC_relativ + LIWC_interrog + LIWC_affect + 
    ##     LIWC_affiliation + LIWC_drives + LIWC_focuspresent + LIWC_insight + 
    ##     LIWC_focuspast + LIWC_percept + LIWC_negemo + LIWC_space + 
    ##     LIWC_ppron + LIWC_power + LIWC_cause + LIWC_tentat + LIWC_hear, 
    ##     data = consp_engage)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5.4586 -1.9549  0.0459  1.6888 11.3338 
    ## 
    ## Coefficients:
    ##                     Estimate Std. Error t value    Pr(>|t|)    
    ## (Intercept)        1.2656009  0.2829135   4.473 0.000007761 ***
    ## LIWC_Clout         0.0171406  0.0040604   4.221 0.000024444 ***
    ## LIWC_social        0.0795352  0.0194704   4.085 0.000044354 ***
    ## LIWC_ipron         0.0966441  0.0190573   5.071 0.000000401 ***
    ## LIWC_time          0.0860787  0.0358748   2.399     0.01643 *  
    ## LIWC_relativ      -0.0195120  0.0307659  -0.634     0.52596    
    ## LIWC_interrog     -0.0277806  0.0407012  -0.683     0.49490    
    ## LIWC_affect       -0.0140949  0.0246395  -0.572     0.56730    
    ## LIWC_affiliation  -0.1589916  0.0341363  -4.658 0.000003231 ***
    ## LIWC_drives        0.0847634  0.0260854   3.249     0.00116 ** 
    ## LIWC_focuspresent  0.0155839  0.0132167   1.179     0.23838    
    ## LIWC_insight      -0.0254405  0.0255903  -0.994     0.32017    
    ## LIWC_focuspast    -0.0007211  0.0175717  -0.041     0.96727    
    ## LIWC_percept       0.0243847  0.0236395   1.032     0.30231    
    ## LIWC_negemo       -0.0712671  0.0300050  -2.375     0.01756 *  
    ## LIWC_space         0.0912854  0.0347866   2.624     0.00870 ** 
    ## LIWC_ppron        -0.0863821  0.0174847  -4.940 0.000000789 ***
    ## LIWC_power        -0.1082461  0.0289965  -3.733     0.00019 ***
    ## LIWC_cause         0.1124763  0.0254058   4.427 0.000009624 ***
    ## LIWC_tentat       -0.0525998  0.0269329  -1.953     0.05084 .  
    ## LIWC_hear          0.0654065  0.0485607   1.347     0.17804    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.473 on 13092 degrees of freedom
    ## Multiple R-squared:  0.02148,    Adjusted R-squared:  0.01999 
    ## F-statistic: 14.37 on 20 and 13092 DF,  p-value: < 0.00000000000000022

``` r
## Checking residuals and variable inflation (collinearity) issues
consp_res <- residuals(consp_lm)
hist(consp_res)
```

![](images/Checking%20linear%20model-1.png)<!-- -->

``` r
qqnorm(consp_res)
```

![](images/Checking%20linear%20model-2.png)<!-- -->

``` r
plot(fitted(consp_lm), consp_res)
```

![](images/Checking%20linear%20model-3.png)<!-- -->

``` r
vif(consp_lm)
```

    ##        LIWC_Clout       LIWC_social        LIWC_ipron         LIWC_time 
    ##          4.409542          7.288148          1.756249          6.119432 
    ##      LIWC_relativ     LIWC_interrog       LIWC_affect  LIWC_affiliation 
    ##         14.846844          1.423397          2.878377          3.626475 
    ##       LIWC_drives LIWC_focuspresent      LIWC_insight    LIWC_focuspast 
    ##          7.664702          2.593067          1.335514          1.892586 
    ##      LIWC_percept       LIWC_negemo        LIWC_space        LIWC_ppron 
    ##          1.603599          2.482252          8.673036          3.402531 
    ##        LIWC_power        LIWC_cause       LIWC_tentat         LIWC_hear 
    ##          4.949332          1.238780          1.469383          1.705756

``` r
## Removing variables with high vif and fitting a new model
consp_lm2 <- lm(engagelog ~ LIWC_Clout + LIWC_social + LIWC_ipron + LIWC_time + LIWC_interrog +
                LIWC_affect + LIWC_affiliation + LIWC_focuspresent + LIWC_insight + 
                LIWC_focuspast + LIWC_percept + LIWC_negemo + LIWC_ppron + LIWC_power +
                LIWC_cause + LIWC_tentat + LIWC_hear, consp_engage)
summary(consp_lm2)
```

    ## 
    ## Call:
    ## lm(formula = engagelog ~ LIWC_Clout + LIWC_social + LIWC_ipron + 
    ##     LIWC_time + LIWC_interrog + LIWC_affect + LIWC_affiliation + 
    ##     LIWC_focuspresent + LIWC_insight + LIWC_focuspast + LIWC_percept + 
    ##     LIWC_negemo + LIWC_ppron + LIWC_power + LIWC_cause + LIWC_tentat + 
    ##     LIWC_hear, data = consp_engage)
    ## 
    ## Residuals:
    ##     Min      1Q  Median      3Q     Max 
    ## -5.3995 -1.9697  0.0554  1.6892 11.3530 
    ## 
    ## Coefficients:
    ##                    Estimate Std. Error t value          Pr(>|t|)    
    ## (Intercept)        1.904529   0.263645   7.224 0.000000000000533 ***
    ## LIWC_Clout         0.020236   0.004029   5.023 0.000000515692689 ***
    ## LIWC_social        0.054936   0.018934   2.901           0.00372 ** 
    ## LIWC_ipron         0.094000   0.019079   4.927 0.000000845309100 ***
    ## LIWC_time          0.074165   0.015709   4.721 0.000002368902769 ***
    ## LIWC_interrog     -0.031730   0.040583  -0.782           0.43432    
    ## LIWC_affect       -0.007669   0.023726  -0.323           0.74653    
    ## LIWC_affiliation  -0.071378   0.023484  -3.039           0.00238 ** 
    ## LIWC_focuspresent  0.019640   0.012970   1.514           0.12998    
    ## LIWC_insight      -0.046356   0.025349  -1.829           0.06746 .  
    ## LIWC_focuspast    -0.001470   0.017562  -0.084           0.93328    
    ## LIWC_percept       0.022617   0.023258   0.972           0.33085    
    ## LIWC_negemo       -0.080409   0.029866  -2.692           0.00710 ** 
    ## LIWC_ppron        -0.083416   0.017453  -4.780 0.000001775898618 ***
    ## LIWC_power        -0.021726   0.015069  -1.442           0.14937    
    ## LIWC_cause         0.106494   0.025013   4.257 0.000020818199287 ***
    ## LIWC_tentat       -0.049112   0.026963  -1.821           0.06856 .  
    ## LIWC_hear          0.073475   0.048509   1.515           0.12988    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 2.477 on 13095 degrees of freedom
    ## Multiple R-squared:  0.01841,    Adjusted R-squared:  0.01714 
    ## F-statistic: 14.45 on 17 and 13095 DF,  p-value: < 0.00000000000000022

``` r
## Checking new model
consp_res2 <- residuals(consp_lm2)
hist(consp_res2)
```

![](images/Checking%20new%20linear%20model-1.png)<!-- -->

``` r
qqnorm(consp_res2)
```

![](images/Checking%20new%20linear%20model-2.png)<!-- -->

``` r
plot(fitted(consp_lm2), consp_res2)
```

![](images/Checking%20new%20linear%20model-3.png)<!-- -->

``` r
vif(consp_lm2)
```

    ##        LIWC_Clout       LIWC_social        LIWC_ipron         LIWC_time 
    ##          4.328265          6.872005          1.755086          1.169945 
    ##     LIWC_interrog       LIWC_affect  LIWC_affiliation LIWC_focuspresent 
    ##          1.411066          2.661058          1.711387          2.489965 
    ##      LIWC_insight    LIWC_focuspast      LIWC_percept       LIWC_negemo 
    ##          1.306614          1.884950          1.547765          2.452138 
    ##        LIWC_ppron        LIWC_power        LIWC_cause       LIWC_tentat 
    ##          3.380267          1.332712          1.197317          1.468431 
    ##         LIWC_hear 
    ##          1.697173

\`\`\`
