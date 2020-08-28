---
layout: post
date: 2019-02-04 19:36
title: Multilingual BERT
description: Scrutinzing Multilingual BERT inside
comments: True
category: 
- deep_learning
- application
tags:
- deep_learning
- application
- language_model
- bert
---

### My Application 

### Opinion
To straighforward, I think M-BERT is merely model with expanded token set, trained to learn general language representation in order to cover more vocabularies in various language. In other words, M-BERT does not seem to learn detailed semantic relationship of other language through fine-tunning with one language, but apply learned structure to tokens from other language. It can be infered that M-BERT would regard corpora from various language as one corpus with various syntactic context.
NLI (Natural Language Inference) task or STS (Semantic Textual Similarity) task on general language such as English, Chinese are closely related to the general representation of language obtained while pre-training NSP(Next Sentence Prediction) task. (Albeit the efficiency of NSP loss is suspected in 'RoBERTa' paper) In this point of view, M-BERT's transferability  is unlikely to work well to domain-specific data, such as engineering log of manufacturing field. Engineering log is written in quite different syntactic structure with that of general language, since more than half of tokens are unique domain words and  many components and spelling are omitted to convey essential information. So, the sentence is not in complete structure or consists of implicit words which can not be expected to learn through wikipedia corpus. 
Engineering log includes Vietnamese and Chinese as well as Korean and English, and there is accumulated target data in English and Korean, but not in  Vietnamese and Chinese, which would be the next target to expand the system. Client does not require multilingual language model explicitly, but model that deal with different languages seems to be required to solve the task for me unless training seperate model for each language with spending high cost in labelling job. M-BERT is one of suggestions to guide how to approach the multilinugal task as well as it showed some possibilities.

#### Reference and Implementation:
- paper: [How multilingual is Multilingual BERT?](https://arxiv.org/abs/1906.01502), 4 Jun 2019 


<!--language-->