---
title: Collaborative-deep-learning-for-recommender-system
tags:
---

recommender system methods:

-   content based
-   collaborative filtering
-   hybrid method



among hybrid method, there are two sub-categories:

-   loosely coupled method:  process the auxiliary information once and then use it to provide features for the CF models. Since information flow is one-way, the rating information cannot provide feedback to guide the extraction of useful features. For this sub-category, improvement often has to ***rely on a manual and tedious feature engineering process***.
-   tightly coupled method:  allow two-way interaction. On one hand, the rating information can guide the learning of features. On the other hand, the extracted features can further improve the predictive power of the CF models
    -   方法之一：Collaborative topic regression (CTR): It is a probabilistic graphical model that seamlessly integrates a topic model, latent Dirichlet allocation (LDA) [5], and a model-based CF method, probabilistic matrix factorization (PMF)
        -   提出：Collaborative Topic Modeling for Recommending Scientific Articles，[解读]()
        -   缺点：the latent representation learned is often not effective enough especially when the auxiliary information is very sparse
    -   改进：此篇文章
    -   