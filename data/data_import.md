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
files, `eval = FALSE` is added to chunk headers for the purpose of
knitting this file. To run these chunks on your own, set to `TRUE` or
remove the argument from the chunk headers as needed.

``` r
LOCO_LFs <- fromJSON(file = "../../LOCO_LFs.json")
```

``` r
LOCO <- fromJSON(file = "../../LOCO.json")
```

``` r
## Exploring elements of list in JSON files; each nested list corresponds to one document
LOCO[1] %>%  str()
LOCO_LFs[1] %>% str()
```

## Converting JSON files into rectangular dataframes

``` r
## Convert LOCO_LFs JSON into tibble
LOCO_LFs_df <- LOCO_LFs %>% 
  tibble()
```

``` r
## Convert LOCO JSON into tibble
LOCO_df <- LOCO %>% 
  tibble()
```

``` r
## Take each element of list-column and make new columns (rectangling)
LOCO_LFs_df <- LOCO_LFs_df %>% 
  unnest_wider(LOCO_LFs)
```

``` r
## Take each element of list-column and make new columns (rectangling)
LOCO_df <- LOCO_df %>% 
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
