---
layout: post
date: 2017-12-23 19:23
title: Attention
description: New paradigm of semantic embedding 
comments: True
category: 
- paper_review
tags:
- nlp
- deep_learning
- attention
---
### TL;DR:
- This paper provided clue to solve long term dependency problem and to develop self-attention, transformer, and BERT, the most popular model in 2019.
- It is undeniable that attention, which supports decoder to search where the relatively significant parts are, is novel approach itself compared to previous one which embed source sentence into one fixed-length vector according to distributional hypothesis. 
- Eventually, attention contributed to broaden model variation of NLP, expanding the existing options that were limited to recurrent network family.
<!--more-->
<br>

### What is encoder-decoder architecture?
Encoder-Decoder architecture is a model in which two networks communicating with each other. Encoder network usually compress its input data into latent vector which represents the feature of input sentence, While decoder network convert input into expected output such as corresponding sentence in other language or next sentence using either latent vector of encoder or new input data for decoder.<br>
In brief, it aims to reconstruct input data into specific output thorugh compression and decompression.<br>
Seq2seq, the special case of encoder-decoder architecture, is named after its using of sentence, the sequential flow of word.<br> 
<br>

### Intro.
The paper suggests the approch to make new context vector, so-called attention vector, which guide the network the words to be shed light on,  Since fixed-length sentence vector is a performance bottleneck; it is hard for fixed-length vector to represent full-semantic, especially in case of long sentence, since  It includes semantic and sequential relationships, but not where to concentrate among tokens. The attention vector can not only be used for improvement of various-length sentence, but also for visualizing where the model did focused on. 
<br>

### How does the model train where to concentrate?
- AS-IS
  - Previous models, RNN based Encoder-Decoder models, learn the joint probabilities of context vector c from encoder and token vectors before timesteps t to predict token at timesteps t. 
- TO-BE
  - Alignment model which takes inputs as hidden state from encoder and output probabilities of decoder computes the score how hidden state is helpful to predict. Alignment model is a feedforward network learning jointly to maximize the score during task. 
  - Attention vector is the soft-aligned score after softmax, which represent the probability each position of output from encoder is helpful to predict output of decoder at corresponding position. 
  - Decoder can selectively retrieve the source word to pay attention to with new context vector, weighted sum of attention vector and decoder output vector.
In brief, the context vector helps decoder to decide which parts of the source sentences to pay attention to, while disengaging encoder from the burden to compress all necessary information into fixed-length vector at the same time, which results in solving the long dependency problem.
<br>

### Conc.
According to the paper, while the performance of vanila recurrent network gets deteriorated when the senquence length over 50, recurrent network with attention shows robust performance to sequence length more than 50. BERT, the most stable sota architecture to date, reduces parameters to learn of its softmax layers using subword tokenizing. It is possible since BERT can take sequence of 512 tokens as input with attention technique. (Attention used in BERT is more tunned one in detail than above.) <br>
Moreover, attention vector, the position-wise weight vector for searching which part to concentrate among the source sentence passed through encoder, additionally provide visualization for qualitative analysis such as  relationship between generated words and each word of source sentence in encoder-decoder architecture, or each word's importance for given downstream task. 
<br>

### Opinion.
The author argues that it is not necessary to encode source sentence into fixed-length vecotr, rather variable-length vector can be more helpful. However, fixed-length vector is necessary when to use the latent vector itself in tasks such as sentence clustering or semantic search. It is unconvincing to use distance metric for vecotrs with different length, since position alignment is not guaranteed; 0th value of vector A can mean tense, while corresponding value of vector B means gender. <br>
<br>

<!--language-->


<!--footer-->
<br>

#### Reference and Implementation:
- paper: [Neural Machine Translation by jointly learning to align and translate](https://arxiv.org/abs/1409.0473), 1 Sep 2014