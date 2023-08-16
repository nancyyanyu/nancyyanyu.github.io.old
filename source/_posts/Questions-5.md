---
title: 'Deep Learning Q&A Part I: UAT, Motivation'
tags: Interview
categories: Machine Learning
mathjax: true
comments: true
abbrlink: c7bd9d66
date: 2019/06/17 15:22:02
---



## Universal Approximation of neural networks

### 1. State the universal approximation theorem? What is the technique used to prove that?

**Universal approximation theorem** (Hornik et al., 1989; Cybenko, 1989) states that a feedforward network with a linear output layer and at least one hidden layer with any “squashing” activation function (such as the logistic sigmoid activation function) can approximate any Borel measurable function from one finite-dimensional space to another with any desired non-zero amount of error, provided that the network is given enough hidden units. 

The universal approximation theorem means that **regardless of what function we are trying to learn, we know that a large MLP will be able to represent this function.**

However, we are not guaranteed that the training algorithm will be able to learn that function. Even if the MLP is able to represent the function, learning can fail for two different reasons. 

1. The **optimization algorithm** used for training may not be able to find the value of the parameters that corresponds to the desired function.
2. The training algorithm might **choose the wrong function due to overfitting**

The universal approximation theorem says that there exists a network large enough to achieve any degree of accuracy we desire, but the theorem does not say how large this network will be.

### 2. What is a Borel measurable function?

Any continuous function on a closed and bounded subset of $R^n$ is Borel measurable and therefore may be approximated by a neural network.

<!--more-->

## Deep Learning motivation

1. What is the mathematical motivation of Deep Learning as opposed to standard Machine Learning techniques?
2. In standard Machine Learning vs. Deep Learning, how is the order of number of samples related to the order of regions that can be recognized in the function space?
3. What are the reasons for choosing a deep model as opposed to shallow model? (1. Number of regions O(2^k) vs O(k) where k is the number of training examples 2. # linear regions carved out in the function space depends exponentially on the depth. )
4. How Deep Learning tackles the curse of dimensionality?



## General questions

1. How will you implement dropout during forward and backward pass?
2. What do you do if Neural network training loss/testing loss stays constant? (ask if there could be an error in your code, going deeper, going simpler…)
3. Why do RNNs have a tendency to suffer from exploding/vanishing gradient? How to prevent this? (Talk about LSTM cell which helps the gradient from vanishing, but make sure you know why it does so. Talk about gradient clipping, and discuss whether to clip the gradient element wise, or clip the norm of the gradient.)
4. Do you know GAN, VAE, and memory augmented neural network? Can you talk about it?
5. Does using full batch means that the convergence is always better given unlimited power? (Beautiful explanation by Alex Seewald: https://www.quora.com/Is-full-batch-gradient-descent-with-unlimited-computer-power-always-better-than-mini-batch-gradient-descent)
6. What is the problem with sigmoid during backpropagation? (Very small, between 0.25 and zero.)
7. Given a black box machine learning algorithm that you can’t modify, how could you improve its error? (you can transform the input for example.)
8. How to find the best hyper parameters? (Random search, grid search, Bayesian search (and what it is?))
9. What is transfer learning?
10. Compare and contrast L1-loss vs. L2-loss and L1-regularization vs. L2-regularization.





**Ref**:

 [machine-learning-interview-questions](https://github.com/Sroy20/machine-learning-interview-questions)