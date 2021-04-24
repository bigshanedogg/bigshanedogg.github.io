---
layout: post
date: 2021-03-31 15:11
title: Switch Transformer
description: Switch Transformer, Pass it to the Experts!
comments: True
category: 
- paper_review
tags:
- nlp
- deep_learning
- language_model
- transformer
---

### TL;DR:
- Switch Transformer is sparsely-active transformer, which can reduce optimizing time by introducing MoE(Mixture of Experts) algorithm and parallelizing parts of model. 
- The advantage of Switch Transformer is that some layers can be parallelized and computation can be accelerated. Efficiency can increase depending on the number of CPU cores. In addition, Switch Transformer shows improvement in quality in low compute resources.
- However, optimizing router and MoE layers may be a cause of training instability at the same time. 

<!--more-->

<br>

### Intro.
GPT-3 shows that tremendously massive data and parameters can be a solution for some NLP tasks. However, unless the resources are guaranteed, we should choose which parts of model to concentrate. Position-wise Feed-Forward layer, the closest layer to softmax layer(output layer), has a quite simple architecture, but hard to conclude that simply to increase the neuron in it is decent approach to improve performance, since the output dimension of pwff layer is ranged from thousands to tens of thousand. Switch Transformer proposed to apply the Mixture of Experts (MoE) algorithm and parallelize the pwff layer in order to improve language modeling performance and to accelerate training. Although It seems hard to expect the suggested accuracy to  downstream tasks in the field, training acceleration through layer parallelization makes it worth trying to apply.<br> 
<br>
### What is Mixture of Experts(MoE)?
It indicates algorithm routing each tensor to different layers, which play the identical role as the existing layer, but specify the weights update per token by optimizing layers separately by routing probability allocated to each token, instead of feeding a set of token tensors to single layer. Each layer is called expert layer, and the most suitable tensor is assigned and computation occurs in parallel.<br>
<br>
### How does Switch Transformer works?

#### A. Basic mechanism
In Switch Transformer, MoE architecture replace position-wise feedforward  layer(pwff layer), sublayer of transformer layer, to expert layers. Expert layers are identically initialized. Weights of each expert layer are updated separately as embedding vector of token with maximum routing probability is allocated after multi-head attention layer(mha layer), sublayer prior to pwff layer, instead of passing all token embedding vectors to one pwff layer. After each expert layer transforms and returns allocated token embedding vector, they are concatenated and passed to output layer(softmax layer).<br> 
<br>
#### B. How to allocate token and handle overflow
As mentioned previously, token embedding vector with the highest routing probability is allocated to expert layer. Existing MoE select k experts to allocate a token. However, Switch Transformers simplify it by fixing k to 1. Thus, routing probability vector H(x) is the output of linear combination between routing variable Wr and token embedding vector x, which is H(x) = x * Wr. Expert capacity is the maximum number of tokens which each expert layer can process. Expert capacity is (timesteps / num_experts) * capacity_factor, and capacity_factor is hyperparameter and set to 1.0 or 1.5 in this paper. If token is allocated to an expert layer that has already exceeded expert capacity, embedding vector of the token is dropped. It means the vector is passed to next layer through residual layer without any linear combination likewise embedding vector of pad token.<br>
<br>
#### C. No-Token-Left-Behind
Just as the constraint to prevent overflow is required, It is necessary to think about how to minimize laid-off experts and allocate tokens to them. If almost tokens are allocated to a single  expert, overflow tokens that exceeds expert capacity will be dropped. No-Token-Left-Behind is an idea to make a token be passed to expert with the next highest routing probability.<br>
<br>
### Exploration and Exploitation
The debate on 'which expert to be allocated token' is related to 'exploration and exploitation' of reinforcement learning. Updating weights through expert layer which is estimated to be a optimal layer to process given token corresponds to exploitation, while searching if there is better alternative by passing token to expert with the second or third highest routing probability, or add noise in calculating routing probability. In this paper, four methods are suggested: Argmax(default), Sample softmax, Input dropout, Input jitter. Input jitter shows the best performance with a slight difference.<br>
<br>
### Conc.
While GPT-3 shows that scailing up can be the simplest way to improve performance, the author argues that Swtich Transformer with as few as two experts improves performance while easily and it is more sample efficient and faster while using the same computational resources.<br>
It sounds novel to allocate a token to different expert by corresponding routing probability. However, in my opinion, it would not be an attractive approach to improve model performance or learning knowledge of language model except optimization of computation through parallelization. It would be more intuitive and effective to increase the number of hidden node inside pwff layer if aiming for segmentation and specification of token embedding.<br>
It is clearly meaningful to introduce MoE architecture to transformer as a dynamic tensor allocation, an alternative of static tensor allocation to a single device, but the communication cost to compute a proper expert layer and allocate/concatenate the result and training instability of large sparse models as the model scale increases should be considered together with the benefit of parallelization.<br>
I wanted to add Switch Transformer as a module to my own KerasTransformer package, but the proper tensor manipulation function, indexing list of keras layers using routing probability tensor, is not supported in tensorflow 1.14  version used by my package. It seems to be the reason that only the mesh-tensor flow code is attached.<br>
<br>
<!--language-->


<!--footer-->
<br>

#### Reference and Implementation:
- paper: [Switch Transformers: Scaling to Trillion Parameter Models with Simple and Efficient Sparsity](https://arxiv.org/abs/2101.03961), 1 Jan 2021 
