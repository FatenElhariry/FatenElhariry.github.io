---
layout: post
title: Natural Language Processing Specialization
description: DeepLearning.AI Course 
author: Faten Elhariry
categories: NLP
tags: Sentiment Analysis, NLP
---

#### Vocabulary & Feature Extraction
  1. in this trial you have to build a vocabulary array with all the words that you have in the document and represent the sentence with 1 & 0 
    - you will end up with a huge array of words which take long time to train you model $|V|$ 
    - you will face some problems 
      - large training time 
      - large prediction time 
  2. then you can use a very simple model which is frequency dictionary
    which represent the word, class and the number of words each word represented in this class   
    <img src="/images/NLP/1.png" />
    _Hence you end up getting the following feature vector [1,8,11][1,8,11]. 11 corresponds to the bias, 88 the positive feature, and 1111 the negative feature._
  ##### word representation 
    - word as a vector 
      where will end up with large word dictionary 
    - word frequancy in a class 
      will be represented with three vectors representing word, count of positive class and count of negative class for each word.
  #### Preprocessing
     I remove all the words that don't add significant meaning to the tweets
    - stemming 
      convert each word to its base stem, which is a set of characeter to define the word and its drivatives it is reducing the size of the corpus and we can use lower case also to reducing the size of the corpus without lossing information 
    - stop words
      you should think carefully before elimnate the stop words based on the given context 
    - When preprocessing, you have to perform the following:

      1. Eliminate handles and URLs
      2. Tokenize the string into words. 
      3. Remove stop words like "and, is, a, on, etc."
      4. Stemming- or convert every word to its stem. Like dancer, dancing, danced, becomes 'danc'. You can use porter stemmer to take care of this. 
      5. Convert all your words to lower case.  
      <img src="/images/NLP/2.png" />
##### mathmatical representation 
  <img src="/images/NLP/3.png" />