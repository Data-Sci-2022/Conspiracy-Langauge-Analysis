#Project Plan

## Conspiratorial Language and Social Media Engagement

### Summary 

Conspiracy theories have found the perfect channel through which to spread to like-minded people, the Internet. The new 88-million word corpus Language of Conspiracy (LOCO) was created with the intention to provide a text collection to study how the language of conspiracy differs from mainstream language. This research project will work with the LOCO to analyze specific features of the language of conspiracy and how they potentially relate to the spread of conspiracies across social media. 

### Data

This project will utilize _LOCO: The 88-million word language of conspiracy corpus_. LOCO is composed of topic-matched conspiracy and mainstream documents harvested from 150 websites. The data is hierarchically structured in JSON files, with each document cross-nested within websites and topics. The documents are also annotated for a rich set of linguistic features and metadata. Among this metadata are measures of social media engagement, specifically Facebook 'shares' and 'reactions.' Utilizing this metadata and the hierarchical organization of the documents, this project will analyze how the conspiratorial nature of language affects social media engagement. 


## Analysis

The authors note that the most representative conspiracy documents, which, compared to other conspiracy documents, display prototypical and exaggerated conspiratorial language, are more frequently shared on Facebook. Using descriptive and inferential statistics as well as R packages such as `tidyverse`, `dplyr` and `ggplot`, this project will analyze the relationships between certain lexical features (all caps, unconventional punctuation, questions directed at the reader, emotional language, and so on) and Facebook shares/reactions across documents labelled conspiratorial versus non-conspiratorial on the same topic. I hypothesize that documents that are richer in 'conspiratorial' lexical features will result in more Facebook shares than their non-conspiratorial counterparts. I also suspect that the Facebook 'reactions' toward documents labelled as conspiratorial will be more polarized. 

## References

Miani, A., Hills, T., & Bangerter, A. (2022). LOCO: The 88-million-word language of conspiracy corpus. _Behavior research methods, 54_(4), 1794–1817. https://doi.org/10.3758/s13428-021-01698-z

Mompelat et al. (2022). How “Loco”is the LOCO corpus? Annotating the language of conspiracy theories. In _Proceedings of the 16th Linguistic Annotation Workshop (LAW-XVI) within LREC2022_, 111–119. European Language Resources Association.
