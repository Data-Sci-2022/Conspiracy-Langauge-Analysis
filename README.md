# Conspiracy Langauge Analysis
By Sen Sub Laban, sen.s@pitt.edu | December 15, 2022

## Project overview

This research project analyzes topic-matched conspiratorial and mainstream documents to understand which lexical features may potentially influence the engagement with and consequent spread of narratives across social media. 

## Where the data comes from

This project utilized LOCO: The 88-million word language of conspiracy corpus. LOCO is composed of topic-matched conspiracy and mainstream documents harvested from 150 websites. The data is hierarchically structured in JSON files, with each document cross-nested within websites and topics. The documents are also annotated for a rich set of linguistic features and metadata. LOCO is made freely available by its authors, licensed under an open access Creative Commons license, permitting use, sharing, adaptation, distribution and reproduction in any medium or format, making it ideal for many avenues of analysis and research such as this one.

Miani, A., Hills, T. & Bangerter, A. LOCO: The 88-million-word language of conspiracy corpus. Behav Res 54, 1794–1817 (2022). https://doi.org/10.3758/s13428-021-01698-z

## Repo organization

- [**Final report**](final_report.md) provides a complete overview of this project, focusing chiefly on analysis, rationale, and conclusions drawn from the data.  

- The [data folder](data/) contains a step-by-step of the coding process as well as all relevant data objects and images. 
  
  - Step 1: Data import [(md)](data/data_import.md) [(rmd)](data/data_import.rmd)
  - Step 2: Data wrangling [(md)](data/data_wrangling.md) [(rmd)](data/data_wrangling.rmd)
  - Step 3: Analysis walkthrough [(md)](analysis_walkthrough.md) [(rmd)](data/analysis_walkthrough.rmd)
  - [Images folder](data/images/) contains all the visualizations generated throughout the analysis 
  
- [Presentation.pdf](presentation.pdf) is a copy of the presentation aid utilized for a presentation about this project on December 8, 2022. 

- [Progress report](progress_report.md) details progress made on this project throughout the semester.

- [Project plan](project_plan.md) was the original plan developed at the start of this project.

- See the [License](LICENSE) to understand what you may and may not do with this project. 
