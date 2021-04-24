---
layout: post
date: 2020-06-06 13:08
title: Multilingual BERT
description: Scrutinzing Multilingual BERT inside
comments: True
category: 
- paper_review
tags:
- nlp
- deep_learning
- language_model
- bert
---

### TL;DR:
- M-BERT(Multilingual BERT) is BERT trained on corpora from various languages.
- M-BERT does not seem to learn systematic transformation of languages. (complicative syntactic/semantic relationship between languages)
- The significant factors of M-BERT's performance
    - Vocabulary Memorization: the fraction of Word overlap between languages and 
    - Mapping new vocabularies onto learned structure
- Merely pre-training general representtation of languages fron unannotated corpora gurantees baseline performance of downstream task in some circumstances.

<!--more-->

<br>

### Intro.
The advantages of Deep Contextualized LM are that the model can learn general language representation with large volume of unlabelled data and the learning can be fine-tunned with smaller labelled data for specific task.<br>
M-BERT, trained on corpora from 104 languages, is the reference model to test zero-shot cross-lingual model transfer. Zero-shot means that the various monolingual corpora used for pre-trainning are not parallel data, which mapped data between languages, and cross-lingual indicates fine-tunning the model to downstream task using one language and evaluating the identical task in other language. There is no other layers appended or structure change to vanilla BERT.<br> 
<br>
### What does mutlilingual LM means?
it indicates that Language Model trained with various monolingual corpora, in order to make model adapt the representation from one language to problems defined in other language.<br>
<br>
###  The hypothesis: How multilingual is M-BERT?

#### A. Vocabulary Memorization?
Before starting sentence level test and further analysis, Simple token-level tests were conducted.
- performance comparison to tasks, NER and POS 
    - NER: Average F1 60~70
    - POS tagging: Average F1 80~90
    
Considering there was no additional parallel data in fine-tunning, the scores seem significant at first glance. However, the substantial token-level performance may be attributed to vocabulary memorization, lexical overlap between languages; the performance may be lifted by cross-lingual trasfer by co-occurring words in corpora.<br>

The comparison between EN-BERT, trained on only English wiki corpus, and M-BERT was conducted to observe transferability according to lexical overlap and possibility to languages written in different scripts (no overlap).
- brief comparison of EN-BERT and M-BERT 
    - EN-BERT: Strong correlation between f1 score and lexical overlap. F1 score is close to zero when there is no overlap.
    - M-BERT: Weak correlation between f1 score and lexical overlap. F1 score is over 40 even when there is no overlap.

The above result shows that vocabulary memorization may affect performance, but it is not the only factor.<br>
<br>
#### B. Topological Similarity between languages?
To check the performance gain according to topological similarity between languages, the author grouped language with WALS features and the order of subject/object/verb and the order of adjective/noun. WALS feautres are structural property of language that describes one aspect of cross-linguistic diversity.<br>
In brief, the experiments show that the similar topoloy of languages may affect the performance , suggesting the low evaluation performance for Japanese after fine-tunning on English corpus.<br> 
It bolsters assumptions that topological similar is significant feature to M-BERT's performance.<br>
In addition, the author claims that M-BERT's multilingual representation is based on mapping learned structure onto new vocabularies, suggesting that the result on Hindi transliterated in Latin script is outperformed by previous works since M-BERT has not learned the language representation of Latin corpus, while result on code-switched Hindi with English performs pretty well. Thus, M-BERT does not seem to learn systematic transformations of those structures to accommodate a target language with different word order, such as English and Japanese.<br> 
<br>
### Exploring feature space of M-BERT
The next experiment is finding the nearest sentence of target sentences using parallel data with no furthre fine-tunning in order to check each layer's learned representation, which called 'nearest neighbor accuracy in this paper.  Accuracy is different for each layer, some layers performs more than 70%.<br> 
<br>
### Conc.
M-BERT shows remarkable performance in task on other language and  task on parallel data such as the nearest neighbor accuracy, even if the model was only pre-trained on unannotated data while not fine-tunned on target language. However, it is likely that the performance is based on mapping new vocabularies in learned structures, not learning the systematic transformations between languages. <br>
<br>
<!--language-->


<!--footer-->
<br>

#### Reference and Implementation:
- paper: [How multilingual is Multilingual BERT?](https://arxiv.org/abs/1906.01502), 4 Jun 2019 