---
layout: post
title: "Learning in Neural Network"
date: 2016-10-03 16:54:23
comments: true
published: true
description: "A review on learning algorithm"
keywords: "NMT, NLP"
excerpt_separator: <!--more-->
categories: tech
tags: 
- NLP, research
---


A review on NMT based on ACL 2016 Tutorial and some current State-of-the-art progresses. Hope to include both theoratical part and practical guide.

<!--more-->

Summarized from [cs231n tutorial](http://cs231n.github.io/neural-networks-3)

# Babysitting Training Process 

When monitoring the neural network training process, we have the followings to think about, and get intuitive idea about how to adjust the parameters:

- Loss function: better seen in log space. May have small violations since mini-batch settings, but it generally should go down.
- Train/Val acc: the gap between them indicates whether you're overfitting.
- Ratio of weights:updates: *Update* means the lr * grad. This is to judge whether the learning rate is suitable, the comfortable ones are arounf 1e-3. Sometimes we can see the norm of all ratio as a general indicator.
- Activation/Gradient distributions: To see whether the initialization is good. e.g. For $$tanh$$, we would like to see distribution of neuron activations between the full range of [-1,1], instead of seeing all neurons outputting zero, or all neurons being completely saturated at either -1 or 1.


# Parameter Update

## SGD

Stocastic Gradient Descent (SGD) is mostly widely used. Also, we will introduce some variants here.

**Vanilla SGD**. Simply change the parameter along the *negative* gradient direction. Since the gradient indicates the direction of increase, but we usually wish to minimize a loss function. (I've been puzzled long by the direction of update! Here's the explanation! Or you can consider $$y = x^2$$ for example.)

**Momentum update**. 

## Annealling Learning Rate 

## Second-order Methods

Hessian matrix update replaces the role of learning rate, adaptive! Second-order gradient depicts the curvature of loss function. But since it involved learge computation cost, several improvements have been proposed using quasi-Newton methods: BFGS, L-BFGS.

Nonetheless, they are still not common in DNN, since L-BFGS required information of the full dataset, not quite workaround in mini-batch.

## Per-parameter Adaptive Learning Rate

"Customize" learning rate for each parameter.

**Adagrad**. Record the norm of all previous gradients. Previously popular parameters receives smaller updates.


```python
# Assume the gradient dx and parameter vector x
cache += dx**2
x += - learning_rate * dx / (np.sqrt(cache) + eps)
```

The square root is important, but no reason. $$\epsilon$$ usually set to 1e-4 to 1e-8. Downside: static learning rate is still too aggresive.

**RMSProp**. It works much better! Almost the same above, except:

```python
cache = decay_rate * cache + (1 - decay_rate) * dx**2
```

```decay_rate``` is usually [0,9, 0.999]. It uses a moving average of squared gradients instead ???

**Adam**. Recently proposed update that looks a bit like RMSProp with momentum. The (simplified) update looks as follows:

```python
m = beta1*m + (1-beta1)*dx
v = beta2*v + (1-beta2)*(dx**2)
x += - learning_rate * m / (np.sqrt(v) + eps)
```

Notice that the update looks exactly as RMSProp update, except the “smooth” version of the gradient m is used instead of the raw (and perhaps noisy) gradient vector dx.

**Adadelta**. A revision without use of learning rate.