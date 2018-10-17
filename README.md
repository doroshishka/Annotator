# Annotator
Annotator Workshop
Penn Treebank tags: https://www.ling.upenn.edu/courses/Fall_2003/ling001/penn_treebank_pos.html
```
library(dplyr)
library(readr)
library(openNLP)
library(openNLPmodels.en)
library(NLP)
library(stringr)
library(tidyr)

getwd()
setwd("directory")

stop <- data_frame(word = c("Â", "â"), lexicon = c("SMART", "SMART")) #characters to remove
```
###### Import and organize CSV file
```
text <- c(read.csv("a.Debate1.csv", header = TRUE, sep = ","))
text_df <- data.frame(text = as.character(text[[3]]), speaker = as.character(text[[2]]))
text_df$text <- data.frame(gsub("Â", '', text_df$text))
colnames(text_df)[1] <- "text"
s <- unlist(text_df$text)

#clinton_text <- text_df[which(text_df$speaker == "C"), ]
#trump_text <- text_df[which(text_df$speaker == "T"), ]
#s_clinton <- unlist(clinton_text$text)
#s_trump <- unlist(trump_text$text)
```

###### TOKEN ANALYZER
```
sent_token_annotator <-Maxent_Sent_Token_Annotator()
word_token_annotator <-Maxent_Word_Token_Annotator()
pos_tag_annotator <-Maxent_POS_Tag_Annotator() #part of speech tagger
```
###### ANNOTATOR
```
a2 <-annotate(s, list(sent_token_annotator, word_token_annotator)) 
a3 <-annotate(s, pos_tag_annotator, a2) #assigns pos tag to s2 by a2 words
a3w <-subset(a3, type == "word") #dataframe of only words (excludes sentences)
tags <-sapply(a3w$features, '[[', "POS") #produces pos tags
table(tags)
```
###### ALIGNS THE ANNOTATIONS WITH THE WORD
```
POStags <- unlist(lapply(a3w$features, `[[`, "POS"))
s_tr = paste0(s, collapse = " ")
sswl <- unlist(lapply(a3, function(x) substr(s_tr, x$start, x$end))) #sentence list based on annotatOR
sswl <- unlist(lapply(a3w, function(x) substr(s_tr, x$start, x$end))) #splits sentence list by words

a4 <- sprintf("%s/%s", sswl, POStags) #word-POS pairs
a5 <- data.frame(sswl = sswl, POStags = POStags) #creates list with two rows (word and tag)
a5[1:20, ]
```

###### Exports files
```
write.csv(a5, "debate_separated.csv")
write.csv(a4, "debate_joinedPOS.csv")
```
