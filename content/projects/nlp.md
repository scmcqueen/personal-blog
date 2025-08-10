+++
date = '2024-05-04T22:02:31-07:00'
draft = true
title = 'Star Trek The Next Generation Quote Identification'
+++

# Star Trek The Next Generation Quote Identification

## SI 630: Natural Language Processing, January-May 2024

In this project, I tackled the task of quote identification, a hybrid task of author identification and speaker identification, of quotes from *Star Trek: The Next Generation* using the natural language processing techniques of multi-label classification and sentence similarity. I found that the Hugging Face multi-label classifier was the most successful method for identifying the character who spoke a quote, with an F-1 score of 0.4516 compared to the baseline of 0.182. Additionally, I concluded that RoBERTa was a more effective tokenizer for the *Star Trek: The Next Generation* quotes than BERT. Though this project does not provide a reliable method for identifying the character who spoke a quote, it offers insights into which *Star Trek: The Next Generation* have the most unique and distinguishable voices.