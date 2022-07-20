---
layout: post
title: Improving Deep Neural Networks
author: Faten Elhariry
categories: "Deep Learning"
tags: "Deep Learning"
---

NN need iterative process from the _idea, coding, Experiment_ to define the mean and the most essensial parts of the NN which can't define by guess like 
  - #Layers 
  - #hidden layers 
  - leaning rate
  - activation function 
  
_those hyperparameters help us to get more powerfull NN_ through **train, dev and test**
**the best practices to divide your data over train, test and validation is**
  - the dataset is small 
    - train/test: 70/30
    - train/test/validation: 60/20/20
  - the dataset is large 
    - you can give smaller amount to the dev and test dataset like 98/1/1%
- mismatched train/test distribution 
  - if you have problem to detect the image of the cat where the training set coming from the webpages with very nice resolution and frames and the test and training uploaded by user where it is plury doen't have a great condition 
  - in this case it is mandatory to have the test and dev dataset from the same distirbution. 

#### Bais and vairance 
  <img src="/images/DL/1.png" />

  - you have to indicator to define the underfitting and the overfitting
    - if the training error is too small and the testing error is so large this mean that you have **overfitting,high variance** like train error/dev error 1/11%
      - linear classifier may underfit the data
    -  if it is 15% for training and 16% for testing this mean that the algorithm has high bais _it give us worse value in testing than training_
    - **high bais and high variance**: it is used in high dimension data due to it has high bais in some places and high variance in other 

#### Basic Recipe for Machine Learning
  - if it high bais 
    - you need to look at the trining data performance 
    - you may need more compelex model 
  - high variance
    - get more data to enable you algorithm to generlized well
    - add regulization 

  _those steps need to be applied itertivaly until reach low bais, low variance_

  