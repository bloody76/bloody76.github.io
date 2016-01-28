---
layout: post
title:  "An attempt to explain the Latent Dirichlet Allocation"
date:   2016-01-27 13:24:40 +0100
categories: machine learning, nlp, topic modeling
---


LDA is a topic modeling technique that aims at understand how topics are arranged in a corpus of documents, based on the co-occurences of the words
in the documents.
It was invented by David M. Blei, Andrew Y. Ng and Michael I. Jordan in this paper (include link to the paper here).

Some key points about the model itself
======================================

- It is part of the **Unsupervised Learning** family of Machine Learning, which means it can take unlabeled data as input and the model
will try to find by himself the hidden structure that describes the topics arrangement. This is great because that kind of data is
easy to find and you can get some as much as you want by scrapping the internet.

- It is a **bag-of-word** model, which means the order of the words in the input is not used. The biggest advantage of that kind of model
is the simplicity. But, of course, loosing the order of the words in a sentence is equivalent to loosing information. A good example,
from the presentation of MetaMind (insert youtube video here), is if we take those two sentences: `White cells destroying a blood infection` and
`A blood infection destroying white cells`. They don't have the same meanings, but they have the same bag-of-words repsentation:
`['a', 'blood', 'cells', 'infection', 'destroying', 'white']`, so we are loosing context information here.

- It is a **generative model**, which means we try to derive the probabilities of all the variables of the models, enabling it
to generate "new data" once the model fitted the data. So the LDA model could generate documents that describes some given topics. But don't
forget it's also a bag-of-word model, so you won't get a human readable text but just a bag of words without meaningful sentences.

How it works
------------

In this section, I will try to describe LDA as much as I can with some interpretations.

The generative process
======================

LDA is a generative model, this is why it is important to first describe the generative process in order to get useful insights about how it works.

From the paper:

- Choose N, the number of words in the document to generate
- Choose $\theta \sim Dirichlet(\alpha)$
- Then, for each of the $N$ words $w_n$:
    - Choose a topic $z_n \sim Multinomial(\theta)$
    - Choose a word $w_n$ according to the topic drawn and put it in our document.

From the given generative process, we can see that the model must be hierarchical with three levels:

- The parameter $\alpha$ represents the first level of our hierarchy, it keeps the information of
how the topics should be distributed in the corpus of documents.
- The parameter $\theta$ is drawn from the Dirichlet distribution and informs us how the topics should
be distributed in our document.
- Finally, for each word, we draw $z_n$ from the $Multinomial(\theta)$ and choose a word that is closed
to what we drawn.

INSERT IMAGES OF THE HIERARCHICAL STUFF HERE.

Once you got your generative process, you can derive the probabilities to generate the words, the documents
and finaly the corpus. I won't write them here because they are awfuly complex and it is not the point of this
post.

Finaly, what the LDA model is giving you after feeding him with many documents is a matrix $B_{i,j}$, that will
gives you the probability that a word $i$ appears in a document talking about subject $j$. From that matrix,
you can of course extract the bests words representing each topic to have hints about what a topic is about.

How to interpret LDA
--------------------

LDA, as a cousin of the PLSA method (Probabilistic Latent Semantic Analysis), is based on the co-occurences of
the words in the documents to find relations between the topics.

The idea is simple, the more you see a word with another, the more they are related to each other. But if
a word is seen with too much other different words, it won't convey a lot of meaning, so we will ignore it.

It makes sense because one tends to use the same words when talking about a subject. And people talking about
the same subjects use the same words. So LDA is just trying to discover the "jargon" of each topics.

###The differences between TF-IDF

The TF-IDF model seems close to LDA because it is look at occurences of words in a document and in the whole
corpus of documents. But, the difference with LDA is how it counts relations between the words: it is not.
TF-IDF doesn't try to look at how close the words are, or, related to each other.

Although, TF-IDF is a very good model, it just doesn't do the same thing, but one might think of using it to
model topics.


When should we use that method ?
--------------------------------

Other methods that do the same thing.
-------------------------------------

Some details
------------

Why are we using the Dirichlet distribution ?
=============================================

Why are we using variational inference ?
========================================

Going further
-------------

A good implementation
---------------------

Talk about the gensim implementation here.

References
----------
