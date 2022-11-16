# Progress report

### 2nd Progress Report 

In order to take a closer look at the dataframes, LOCO_df and LOCO_LFs_df (tibbles which are 96,743 x 94 (559 Mb) and 96,743 x 288 (217.8 Mb), respectively), I split the dataframes to take the first 50 rows of each and create smaller, sample dataframes. I stored these as Rds files for easy tinkering and exploration. 

Using these smaller tibbles I identified various columns that are not useful for this project. In order to shrink down the size of the dataframes, I used `dplyr` to select only the relevant columns. For example, columns such as 'txt', which contained the entire documents as raw strings, were removed since they were bloating the data considerably. Thus, the size of the data were reduced: LOCO_df shrunk from 559 Mb to 25.1 Mb and LOCO_LFs_df from 217.8 Mb to 74.6 Mb.

Following the selection of the necessary columns, I decided to join the two dataframes into a single tibble for ease of analysis. The dataframes were successfully joined by doc_id, resulting in a final dataframe ready for analysis: LOCO_final. This dataframe is a tibble consisting of 96,743 observations (documents) of 103 variables, stored as LOCO_final.Rds. 

This data wrangling process, including some descriptive statistics on LOCO_final, is available [here](data/data_wrangling.md).

#### Sharing plan & Licence

The sharing plan for this project's data has not changed since the first progress report. LOCO and all related materials are made freely available by its authors under basic copyright, with no licence to be found. The data does not contain any type of sensitive or personal information from its source, and I will not be contributing any data of the sort, so I plan to share this project's data and analysis in its entirety. 

As such, I've chosen the MIT Licesne and updated the [License](/LICENSE) file accordingly. The MIT License is short and to the point. It lets people do almost anything they want with this project. 

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

