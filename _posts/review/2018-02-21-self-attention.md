---
layout: post
date: 2018-02-21 16:24
title: Self-Attention
description: Embedding contextual information into matrix 
comments: True
category: 
- paper_review
tags:
- nlp
- deep_learning
- attention
---
### TL;DR:
- Previous attention needs source vector to conjuncture the relevance with task. In encoder-decoder architecture, an output of encoder is source vector while an output of decoder is target task vector.
- Self-Attention embeds contextual information - each tokens' significance in given task - into a matrix rather than a vector, assuming that there can be more than 1 contextual weight vector for 1 sentence, While former Attention embeds contextual information into vector.
- Self-Attention helps model to pay attention to significant parts of sentence for target task relieving some long-term memorization burden from LSTM, and provides attention matrix for visualization. 
<!--more-->
<br>

### Catch up where sentence embedding is now (2017.3)
Although there are some approaches raised for sentence embedding task, the key for a number of sentence-level NLP tasks. Likewise other tasks, there are two types of approaches to sentence embedding, unsupervised and supervised.

- Unsupervised Approaches: 
  - SkipThought vector
  - ParagraphVector
  - Sequential Denoising
  - FastSent
  - Autoencoders-based approaches (Recursive Autoencoders or Variational Recurrent Autoencoder (AUTOENCODER는 이 링크 참조 VRAE)
- Supervised Approaches:
  - Extract output of the last layer as a sentence vector after fitting into downstream task.
  - The most popular way: max or average pooling of the last hidden state of recurrent model

Self-Attention is for optmizing sentence embedding in supervised manner by adding it after the last layer of recurrent model, as well as for improving the performance of given downstream task. 
<br>

### Intro.
Self-Attention is an embeddable layer rather than an architecture inself. It was spotlighted after novel architecture 'Transformer' has invented since Each layer of Transformer consists of only self-attention layer to match the name 'Attention is all you need.' Moreover, it provides interpretable attention matrix to help comprehend model learning process. 
<br>

###  What's different with previous Attention mechanism?
- Previous attention needs source vector to conjuncture the relevance with task. In encoder-decoder architecture, an output of encoder is source vector while an output of decoder is target task vector.
- Self-Attention embeds contextual information - each tokens' significance in given task - into a matrix rather than a vector, assuming that there can be more than 1 contextual weight vector for 1 sentence, While former Attention embeds contextual information into vector.
<br>

### How to make attention matrix?
<br>

#### A. Structure
Self-Attention is composed of 2-layer feed forward network and specific loss to optimize inter-independent rows of r-by-n matrix  A . r is a hyperparameter to decide the number of contextual aspects that model should optimize, and n is the length of input sequence(timesteps). Inter-independece means that positional informations rows imply are not duplicated. To keep inter-independence between rows, the model use substraction between dot product of   A  and identity matrix I instead of KL divergence. <br>
Matrix  M  is product of attention matrix  A  and hidden state  H  which is concatenation of hidden state vectors from bidirectional LSTM. Embedding self-attention stands for feeding this matrix  M  to downstream task layer such as softmax or regression layer instead of  H .
<br>

#### B. Penalization Term
The purpose of  M  is to imply r aspects of attention, and proper penalization term is required to ensure inter-independent of rows. 
With two reasons below, The author do not use Kullback Leibler divergence(KL divergence), the most popular way to evaluate similarities of two probability distribution.

1. It is unstable to optimize sparse matrix like  A .
   -  A  has a lot of sufficiently small or even zero values at different softmax output unit, and it deteriorates training stability
2. KL divergence does not ensure complete inter-independent as context vectors.
   - Although maximizing KL divergence optimize to make rows different each other, it can not make each row focus on single word. 
   - At worst, even if KL divergence of two rows has been maximized, two rows may focus on semantically similar words. It can results into degradation of multi-hop performance.

So, new penalization is  minimizing \lVert(\mathbf{A}\mathbf{A}^\intercal-\mathbf{I})\rVert_F^2
Here, subtracting  I  is for preventing A from focusing on same part of sentence, and \lVert \bullet \rVert_F stands for the Frobenius norm of a matrix, which is identical to  L2  norm in this case.
<br>

### Exploring feature space of M-BERT
The next experiment is finding the nearest sentence of target sentences using parallel data with no furthre fine-tunning in order to check each layer's learned representation, which called 'nearest neighbor accuracy in this paper.  Accuracy is different for each layer, some layers performs more than 70%.<br> 
<br>

### Conc.
Attention layer is located right after the last convolutional layer or the last cell of lstm layer, thus right efore layer for downstream task, such as softmax or regression layer. Self-Attention helps model to pay attention to significant parts of sentence for target task relieving some long-term memorization burden from LSTM. In addition, it also provides visualization since the optimized matrix can be regarded as heatmap of each token of given sentence per context - visualization for interpretable sentence embedding as a side effect. 
<br>

<!--language-->


<!--footer-->
<br>

#### Reference and Implementation:
- paper: [A Structed Self-attentive Sentence Embedding](https://arxiv.org/abs/1703.03130), 9 Mar 2017