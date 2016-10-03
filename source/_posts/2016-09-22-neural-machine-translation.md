---
layout: post
title: "Neural Machine Translation Tutorial"
date: 2016-09-22 16:39:23
comments: true
published: true
description: "A review on NMT based on ACL 2016 Tutorial"
keywords: "NMT, NLP"
excerpt_separator: <!--more-->
categories: tech
tags: 
- NLP, research
---


A review on NMT based on ACL 2016 Tutorial. Including the theoratical part and practical guide.
<!--more-->

More rec: [nmt practical tips](https://github.com/neubig/nmt-tips)

# Inrtroduction on (Neural) Machine Translation

TODO:

Quickly review the SMT.

One sentence def of Neural Machine Translation (NMT): 

> Neural Machine Translation is the approach of modeling the entire MT process via one big artificial neural network.

## Quick View on NMT:

* Purposed in 2014 [Sutskever et al., Bahdanau et al.], widely adopted now!
* End-to-end training. All parameters tuned simultaneously.
* Larger context


<Need info on SMT/phrase-based MT!!>
[](https://alopez.github.io/papers/survey.pdf)

## Language Model Revisit

To know the basic structure of NMT, which is a typical encoder-decoder structure, we have to first know the neural language model. If you know about both CBOW and RNNLM, you can skip this part.

Language model is basically a probability $$ p(\mathbf{x}) = p(x_1, x_2, \dots, x_n) $$ for a given sentence, so that it's higher for correct ones. 

Traditional LM converts the probability, assuming each word only depends on its precedents: $$ p(\mathbf{x}) = \prod_{t}p(x_t \vert x_1, x_2, \dots, x_{t-1}) $$. Under the Markov assumption, we can further convert the equation like: $$ p(x_t \vert x_1, \dots, x_{t-1}) = p(x_t \vert x_{t-n}, \dots, x_{t-1}) $$, where $$n$$ is the order of Markov chain.

### Prob 1: Too Large to Store

According to n-gram model, we have to store values associated with $$ n $$ words, which goes exponentially large with vocabulary size $$ V = 100,000 $$. That's not a cost we can afford. On the other hand, few of them are non-empty entries according to syntactic regulation. 

### Sol 1: Probabilistic LM

In 2003, Bengio first introduced the neural language model, which he represented the word as a *dense* and distributed value. They tried to minimize the n-gram probability by first encoding the first n-gram context ($$ n \times d $$) into a $$d$$-dim vector, then using a $$ \vert V \vert \times d $$ matrix to recover the probability for next word. Detailed process is described below:

![]({{site.url}}/images/nmt-nlm.png)

Further revision are advanced as CBOW, Skip-gram.

### Prob 2: Short-term Context

Under the Markov assumption, only limited number of preceded words are considered as context, which may not capture some long-term dependencies, such as the reference of a person after a long modifier.

### Sol 2: Recursive Neural Network (RNN)

Let's remove the Markov assumption and calculate the probability directly. Using a recursive function, we can encode all previous information by keeping a hidden state $$ h_t $$ that captures all previous words as a dense vector. At each time step, the hidden state will output a probability indicating the next word, thus this probability is conditioned on all previous words. 

![]({{site.url}}/images/nmt-rnnlm.png)

# RNN Revisit

This section helps readers to go through basic knowledges about RNN, involving training procudure, improvement on RNN structure, and decoding methods.

## Training in RNN

Basic stocastic gradient descent can be applied here. But the calculation may be different since it's a recursive function. Due to its recursive property, vanilla RNN usually suffers from *vanishing/exploding gradient* problems as the gradient passes through $$ \frac{\partial{h_t}}{\partial{h_{t-1}}}  $$. To avoid the exploding gradient, we can use *clipping* method so that we set a bound to the gradient. For vanishing gradient, we can either: initialize $$W$$ to identity matrix, or: use ReLU insteaf of Tanh.

To get the sense in mathamatical ways, see post [here](http://www.wildml.com/2015/10/recurrent-neural-networks-tutorial-part-3-backpropagation-through-time-and-vanishing-gradients/). 

Another thing to notice is the various length of sentences. It's true that RNN can handle various length at ease, but in practice, due to the storage issue, we'd better pad 0's at the end so that they are in same length. When back-propagate, we can use masking to omit these 0s. 

## GRU and LSTM

So far, RNN may not capture long-term memory as we expect. But modifications like GRU as LSTM can. I would not cover too much on these, because there has been so many tutorials on this. But basically, they modified the single-unit hidden state to a more complicated integration of units. They may use other units trying to *forget* or *reset* certain values to keep important messages passing along.

## Encoder-Decoder

A modification of RNN strucutre is Encoder-Decoder. It first encodes the input (may be images, sentences, etc.) to a vector using RNN, then decode the output message per time step. Below shows this structure:

![]({{site.url}}/images/nmt-ende.png)

Sometimes we may add extra inputs to decoder, like the original vector, attention vector, etc.

## Decoding Strategies

Decoding may not be easy, because what we want is a globally optimized sequence, while we only generate one at a time. We would describe 3 attempts below:

1. Ancestral Sampling: sample a term from the softmax distribution each time. 
    It's unbiased, but introduce high variance, and pretty inefficient??
2. Greedy Search: get the best each time
    Efficient but suboptimal
3. Beam Search:
    Dynamically preserve a set of Top-K result. When expanded, $$K \times \vert V \vert$$ results are evaluated. Much better results, but computationally expensive

# Advancing NMT

## Vocabulary Aspect
 
Goal: extend the vocabulary coverage.

From $$\vert V \vert$$ = 50K to 500K!

**On Using Very Large Target Vocabulary for Neural Machine Translation**, Sébastien Jean et al, ACL'15. 

This paper introduces a way to maintain a larger vocabulary.

On training: it sequentially selects samples (added to a mini-batch) so that there are only $$\vert V' \vert$$ (usually 50K) distinct words in this mini-batch. We can thus only update these words.

On testing: preserve $$K$$ most frequent words (usually 15K-50K) via unigram prob. For each source word, we select $$K'$$ candidate target words (via conditional prob, usually 10-20). The final vocab for each words is $$K + K'$$.

Other papers include:

**BlackOut: Speeding up Recurrent Neural Network Language Models with very Large Vocabularies** Ji et al, ICLR'16. It's a good survey over many techniques.

**Simple, Fast Noise Contrastive Estimation for Large RNN Vocabularies**, Zoph et al, NAACL'16. It uses the same samples per minibatch. but it's GPU efficient

Another Technique is the copy machenism, which is a idea from SMT. When acknowledged that many out-of-vocab words are specific names, organizations or numbers, we can use a dictionary to translate or simply copy the words.

**Addressing the Rare Word Problem in Neural Machine Translation**, Thang Luong et al, ACL’15.

This paper views the NMT as a black box, and trains a model that learns alignment between source and target sentence, then uses a dictionary to post-process the target sentence.

Note that copy machenism is also useful in text summarization [Gu, Lu, Li, Li, ACL’16], [Gulcehre, Ahn, Nallapati, Zhou, Bengio, ACL’16] and semantic parsing [Jia, Liang, ACL’16].

## Memory Aspect

Goal: translate long sentences better. (Because longer sentece may not be efficiently encoded into a single vector, some of the information may be smoothed out.)

Here comes the attention mechanism.

**Neural Machine Translation by Jointly Learning to Translate and Align**, Dzmitry Bahdanau et al, ICLR'15.

**Effective Approaches to Attention-based Neural Machine Translation**, Thang Luong et al, EMNLP’15.

These two papers are mainly addressing this issue. Accoring to paper, we define a score function $$score(h_t, h_s)$$, where $$t$$ is the current decoding step, $$s$$ is each source word. Then we integrate them into a *context vector* as a input to decoding unit. 

Using attention, we can translate person names and double-negation well.

## Language Complexity Aspect

Goal: handle more language variations, due to open and large vocab in web data.

Idea is to operate on sub-word level, which is a trending idea in language model.

**Finding Function in Form: Compositional Character Models for Open Vocabulary Word Representation**, Ling et al, EMNLP’15. They use char-based BiLSTM as word representation.

**Character-Aware Neural Language Models**, Yoon Kim et al, AAAI 2016. They use ConvNet instead of BiLSTM. Also a Highway layer which is similar to GRU but applied vertically.

Instead of exploiting smaller units, some use hybrid architectures, both word and char level. [Costa-Jussà & Fonollosa, ACL'16], [Luong & Manning, ACL'16]

<More later>

## Data Aspect

Goal: utilize more data sources.

Multi-lingual

Exploit monolingual data

**On Using Monolingual Corpora in Neural Machine Translation**, Gulcehre et al, arXiv2015.

Integrate monolingual language models with the original encoder. Shallow score interpolation or deep fusion.

<image>

Multi-task


# Future of NMT

## Multi-task learning

Multi-lingual translation: project to a universal language-agnostic space: encoder to a context vector, shared attention, decoder from context vector and attention.

Multi-modal: project to a modality-agnostic space! either languages, images or speech signal!

## Larger Context

## Mobile Device

Abigail See, Thang Luong, Chris Manning. Compression of Neural Machine Translation Models via Pruning. CoNLL’16.

Model pruning of small weights: 40% pruning without loss much.
pruning + retraning: 80%!

Yoon Kim, Alexander M. Rush. Sequence-level knowledge distillation. EMNLP’16.

Using a large teacher network to teach a smaller student network, by matching the final softmax distributions. 

## Beyond MLE

Note that doing inference in decoder is not like training via MLE loss!

Incorporate inference into training:

Stochastic inference
- Policy gradient [Ranzato et al., ICLR2016; Bahdanauet al., arXiv2016]
- Minimum risk training [Shen et al., ACL2016]

Deterministic inference
- Learning to search [Wiseman & Rush, arXiv2016]