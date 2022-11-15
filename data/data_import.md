Data import
================

-   <a href="#data-included-in-the-loco-corpus"
    id="toc-data-included-in-the-loco-corpus">Data included in the LOCO
    corpus</a>
-   <a href="#reading-in-relevant-data"
    id="toc-reading-in-relevant-data">Reading in relevant data</a>
-   <a href="#data-import" id="toc-data-import">Data import</a>
-   <a href="#converting-json-files-into-rectangular-dataframes"
    id="toc-converting-json-files-into-rectangular-dataframes">Converting
    JSON files into rectangular dataframes</a>
-   <a href="#saving-dataframes-as-rds-files"
    id="toc-saving-dataframes-as-rds-files">Saving dataframes as Rds
    files</a>

## Data included in the LOCO corpus

1.  *LOCO.json* (587.6 MB): a JSON (JavaScript Object Notation) file
    containing the LOCO corpus itself. 96,746 rows (documents) × 20
    columns (see Table 6)

2.  *website_metadata.json* (55.3 KB): a JSON file containing websites’
    metadata. 150 rows (websites) × 18 columns (see Table 7)

3.  *LOCO_LFs.json* (573.1 MB): a JSON file containing the full set of
    lexical features. 96,746 rows (documents) × 288 columns
    (NEmpath = 194; NLIWC = 93)

4.  *topic_gamma.json* (963.7 MB): a JSON file containing topics’ gamma
    values. 96,746 rows (documents) × 600 columns (topics)

5.  *topic_by_time.pdf* (169.6 MB): a PDF file containing plots of
    topics’ gamma values over time (from 1995 to 2020). It contains 600
    pages.

6.  *topic_description.json* (188.2 KB): a JSON file containing detailed
    descriptions of topics. 600 rows (topics) × 12 columns (see SM6)

## Reading in relevant data

Excluding 3 files from the original corpus: topic_gamma.json,
topic_description.json, website_metadata.json:

1.  topic_gamma.json is a matrix that contains all gamma values for each
    topic for each document

2.  topic_description.json includes descriptions for each of the 600
    topics including the top 15 terms ordered by beta weight, number of
    documents in which the topic has the highest gamma, highest
    correlation with other topics, and highest correlation with lexical
    features (LIWC and Empath).

3.  website_metadata.json doesn’t provide data connected to any
    individual conspiracy documents, just metadata about various
    websites from which these documents were pulled

Because this analysis will focus on lexical features and social media
shares regardless of the topic of the document, we are only importing
the LOCO_LFs.json and LOCO.json data. - [LOCO dataset variables
descriptions](https://link.springer.com/article/10.3758/s13428-021-01698-z/tables/6)

## Data import

**Note:** Due to the large size and processing demands of the raw data
files, chunks have been cached and `eval = FALSE` added to chunk headers
for the purpose of knitting this file. To run these chunks on your own,
set to `TRUE` or remove the argument from the chunk headers as needed.

``` r
LOCO_LFs <- fromJSON(file = "../../LOCO_LFs.json")
```

``` r
LOCO <- fromJSON(file = "../../LOCO.json")
```

Exploring the elements of lists in JSON files; each nested list
corresponds to one document

``` r
LOCO[1] %>%  str()
```

    ## List of 1
    ##  $ :List of 20
    ##   ..$ doc_id                   : chr "C00001"
    ##   ..$ URL                      : chr "https://humansarefree.com/2016/12/the-conspiracy-against-lt-col-michael-aquino-satanic-pedophile-clinton-reagan"| __truncated__
    ##   ..$ website                  : chr "humansarefree.com"
    ##   ..$ seeds                    : chr "michael.jackson.death"
    ##   ..$ date                     : chr "2016-12-30"
    ##   ..$ subcorpus                : chr "conspiracy"
    ##   ..$ title                    : chr "The 'Conspiracy' Against Lt. Col. Michael Aquino — Satanic Pedophile (Clinton, Reagan, Bush, Cheney, Kissinger)"
    ##   ..$ txt                      : chr "For those who don’t know, Michael Aquino was a Psychological Warfare Specialist in the US Army from 1968 until "| __truncated__
    ##   ..$ txt_nwords               : num 4075
    ##   ..$ txt_nsentences           : num 160
    ##   ..$ txt_nparagraphs          : num 120
    ##   ..$ topic_k100               : chr "k100_24"
    ##   ..$ topic_k200               : chr "k200_75"
    ##   ..$ topic_k300               : chr "k300_192"
    ##   ..$ mention_conspiracy       : num 8
    ##   ..$ conspiracy_representative: logi FALSE
    ##   ..$ cosine_similarity        : num 0.177
    ##   ..$ FB_shares                : num 13
    ##   ..$ FB_comments              : num 2
    ##   ..$ FB_reactions             : num 4

``` r
LOCO_LFs[1] %>% str()
```

    ## List of 1
    ##  $ :List of 288
    ##   ..$ doc_id                      : chr "C00001"
    ##   ..$ LIWC_WC                     : num 4051
    ##   ..$ LIWC_Analytic               : num 88.9
    ##   ..$ LIWC_Clout                  : num 77
    ##   ..$ LIWC_Authentic              : num 14
    ##   ..$ LIWC_Tone                   : num 5.22
    ##   ..$ LIWC_WPS                    : num 22.3
    ##   ..$ LIWC_Sixltr                 : num 27.2
    ##   ..$ LIWC_Dic                    : num 79.3
    ##   ..$ LIWC_function               : num 46.6
    ##   ..$ LIWC_pronoun                : num 9.78
    ##   ..$ LIWC_ppron                  : num 4.2
    ##   ..$ LIWC_i                      : num 0.17
    ##   ..$ LIWC_we                     : num 0.42
    ##   ..$ LIWC_you                    : num 0.02
    ##   ..$ LIWC_shehe                  : num 2.42
    ##   ..$ LIWC_they                   : num 1.16
    ##   ..$ LIWC_ipron                  : num 5.58
    ##   ..$ LIWC_article                : num 6.96
    ##   ..$ LIWC_prep                   : num 15.8
    ##   ..$ LIWC_auxverb                : num 6.81
    ##   ..$ LIWC_adverb                 : num 3.33
    ##   ..$ LIWC_conj                   : num 6.07
    ##   ..$ LIWC_negate                 : num 0.59
    ##   ..$ LIWC_verb                   : num 10.5
    ##   ..$ LIWC_adj                    : num 4.32
    ##   ..$ LIWC_compare                : num 2.72
    ##   ..$ LIWC_interrog               : num 1.63
    ##   ..$ LIWC_number                 : num 2.47
    ##   ..$ LIWC_quant                  : num 2.2
    ##   ..$ LIWC_affect                 : num 4.81
    ##   ..$ LIWC_posemo                 : num 1.41
    ##   ..$ LIWC_negemo                 : num 3.36
    ##   ..$ LIWC_anx                    : num 0.27
    ##   ..$ LIWC_anger                  : num 1.48
    ##   ..$ LIWC_sad                    : num 0.1
    ##   ..$ LIWC_social                 : num 9.73
    ##   ..$ LIWC_family                 : num 0.2
    ##   ..$ LIWC_friend                 : num 0.12
    ##   ..$ LIWC_female                 : num 0.62
    ##   ..$ LIWC_male                   : num 2.42
    ##   ..$ LIWC_cogproc                : num 9.48
    ##   ..$ LIWC_insight                : num 2.3
    ##   ..$ LIWC_cause                  : num 2.2
    ##   ..$ LIWC_discrep                : num 0.49
    ##   ..$ LIWC_tentat                 : num 1.58
    ##   ..$ LIWC_certain                : num 1.68
    ##   ..$ LIWC_differ                 : num 2.02
    ##   ..$ LIWC_percept                : num 1.23
    ##   ..$ LIWC_see                    : num 0.79
    ##   ..$ LIWC_hear                   : num 0.3
    ##   ..$ LIWC_feel                   : num 0.1
    ##   ..$ LIWC_bio                    : num 1.6
    ##   ..$ LIWC_body                   : num 0.32
    ##   ..$ LIWC_health                 : num 0.77
    ##   ..$ LIWC_sexual                 : num 0.62
    ##   ..$ LIWC_ingest                 : num 0.05
    ##   ..$ LIWC_drives                 : num 8.89
    ##   ..$ LIWC_affiliation            : num 1.21
    ##   ..$ LIWC_achieve                : num 0.86
    ##   ..$ LIWC_power                  : num 6.02
    ##   ..$ LIWC_reward                 : num 0.39
    ##   ..$ LIWC_risk                   : num 0.67
    ##   ..$ LIWC_focuspast              : num 5.23
    ##   ..$ LIWC_focuspresent           : num 3.68
    ##   ..$ LIWC_focusfuture            : num 0.42
    ##   ..$ LIWC_relativ                : num 12.9
    ##   ..$ LIWC_motion                 : num 1.21
    ##   ..$ LIWC_space                  : num 7.8
    ##   ..$ LIWC_time                   : num 4.07
    ##   ..$ LIWC_work                   : num 3.36
    ##   ..$ LIWC_leisure                : num 0.42
    ##   ..$ LIWC_home                   : num 0.27
    ##   ..$ LIWC_money                  : num 0.79
    ##   ..$ LIWC_relig                  : num 0.99
    ##   ..$ LIWC_death                  : num 0.47
    ##   ..$ LIWC_informal               : num 0.25
    ##   ..$ LIWC_swear                  : num 0
    ##   ..$ LIWC_netspeak               : num 0.1
    ##   ..$ LIWC_assent                 : num 0.02
    ##   ..$ LIWC_nonflu                 : num 0.15
    ##   ..$ LIWC_filler                 : num 0
    ##   ..$ LIWC_AllPunc                : num 14.6
    ##   ..$ LIWC_Period                 : num 4.47
    ##   ..$ LIWC_Comma                  : num 5.63
    ##   ..$ LIWC_Colon                  : num 0.05
    ##   ..$ LIWC_SemiC                  : num 0
    ##   ..$ LIWC_QMark                  : num 0.02
    ##   ..$ LIWC_Exclam                 : num 0.02
    ##   ..$ LIWC_Dash                   : num 0.99
    ##   ..$ LIWC_Quote                  : num 0.44
    ##   ..$ LIWC_Apostro                : num 1.28
    ##   ..$ LIWC_Parenth                : num 1.23
    ##   ..$ LIWC_OtherP                 : num 0.44
    ##   ..$ Empath_help                 : num 0.002
    ##   ..$ Empath_office               : num 0.003
    ##   ..$ Empath_dance                : num 0
    ##   ..$ Empath_money                : num 0.0022
    ##   ..$ Empath_wedding              : num 0.0022
    ##   .. [list output truncated]

## Converting JSON files into rectangular dataframes

``` r
## Convert LOCO_LFs JSON into tibble
LOCO_LFs_df <- LOCO_LFs %>% 
  tibble() %>% 
## Take each element of list-column and make new columns (rectangling)
  unnest_wider('.')
```

``` r
## Convert LOCO JSON into tibble
LOCO_df <- LOCO %>% 
  tibble() %>% 
## Take each element of list-column and make new columns (rectangling)
  unnest_wider('.')
```

## Saving dataframes as Rds files

``` r
if (!dir.exists("data")) {
  dir.create("data")
}
write_rds(LOCO_df, "data/LOCO_df.Rds")
write_rds(LOCO_LFs_df, "data/LOCO_LFs_df.Rds")
```

**Note:** these Rds are currently way too large (\> 25mb) for Github’s
liking; in the next stages of data wrangling they will hopefully be the
proper size for upload.
