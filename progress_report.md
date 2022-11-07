# Progress report

### 1st Progress Report 

Completed the import of selected JSON (JavaScript Object Notation) files into R, excluding 3 files from the original corpus: topic_gamma.json, topic_description.json, website_metadata.json. Because this analysis will focus on lexical features and social media shares regardless of the topic of the document, I plan to rely only on data from *LOCO_LFs.json* and *LOCO.json*.

1. *LOCO.json* (587.6 MB): a JSON file containing the LOCO corpus itself. 96,746 rows (documents) × 20 columns 

1. *LOCO_LFs.json* (573.1 MB): a JSON file containing the full set of lexical features. 96,746 rows (documents) × 288 columns (NEmpath = 194; NLIWC = 93)

The JSON files were successfully converted into rectangular form using `tibble()` and `unnest_wider()`. Due to the sheer size of the data (over half a gigabyte per JSON file), processing the files takes up a considerable amount of memory (and time). My next step in harnessing the data will be to utilize `dplyr` to remove columns and information that is uninformative or that won't be used in my particular analysis, hopefully reducing the large size of the data so that it is less demanding to work with. See data import process [here](data/data_import.md).  

During this process, I ran into a major issue with the too-large .Rds files appearing in git's commit history and wound up having to clone this repo.

#### Sharing plan
Since LOCO and related datasets are made freely available by its authors, I plan to share this project's data and analysis in its entirety. The corpus does not contain any type of sensitive information, and neither will the output of my work and analysis.

### 10.25.2022

Redefined scope of project to focus on the relationships between lexical features and social media engagement. 
Updated project plan accordingly. 

### 10.13.2022

Created github repo

