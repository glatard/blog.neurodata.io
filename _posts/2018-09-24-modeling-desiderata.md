---
layout: post
title:  "Estimator Desiderata"
date:   2018-09-23 14:27:57 -0400
tags: bits
author: Joshua Vogelstein
---


When designing an estimator (or a learner, as machine learning people say), there are a number of desiderata to consider.  I believe the following is a [MECE list](https://en.wikipedia.org/wiki/MECE_principle) (mutually exclusive and collectively exhaustive).  However, if you have other desiderata that I have missed, please let me know in the comments.  Recall that an estimator, takes data as input, and outputs an *estimate*.  Below is my list:

1. **Sufficiency**: The estimate must be sufficient to answer the underlying/motivating question.  For example, if the question is about the average height of a population, the estimate must be a positive number, not a vector, not an image, etc.  This is related to [validity](https://en.wikipedia.org/wiki/Validity_(statistics)).

2. **Uncertainty**:  Point estimates (for example, the maximum likelihood estimate), are often of primary concern, but uncertainty intervals around said point estimates are often required making effectively using the estimates.  Point estimates of confidence intervals, and densities, are also technically point estimates.  However, such point estimates fundamentally incorporate a notion of uncertainty, so they satisfy this desiderata.  We note, however, that while some estimators have uncertainty estimates with theoretical guarantees, those guarantees depend strongly on the model assumptions.

<!-- In many cases, even today's best estimators are "mis-calibrated". For example, if an estimator learns a posterior over a two-class problem, and then a test example is provided that looks nothing like any of the training data, modern classifiers will often be highly confident, when they "should" say that they do not know (see Robustness and Stability below).  -->

4. **Consistency**:  We prefer that, under the model assumptions, for any finite sample size, that the expected estimate is equal to the assumed "truth", i.e., is *unbiased*.  If this unbiased property holds asymptotically, the estimator is said to be  *consistent*.  This property is graded, meaning estimators can yield estimates with any non-negative bias, and we desire that it is small.   


6. **Efficiency**:  Assuming that the estimator converges to *something*, all else being equal, we desire that it converges "quickly".  The typical metric of statistical efficiency is the variance (for a scalar values estimate), or the Fisher information more generally.  Ideally the estimator is *efficient*, meaning, under the model assumptions, it achieves the minimal possible variance.  To a first order approximation, we often simply indicate whether the convergence rate is polynomial, polylog, or exponential.  Note that efficiency can computed with respect to the sample size, as well as the dimensionality of the data (and both the "intrinsic" and "extrinsic" dimensionality). Note that there are many difference notions of [convergence of random variables](https://en.wikipedia.org/wiki/Convergence_of_random_variables), and the estimators are indeed random variables (because their inputs are random variables). Because perfect efficiency is available only under the simplest models, *relative efficiency* is often more important, in practice.  Probably almost correct learning theory, as introduced in [this book](http://a.co/d/bYJlTWA) is largely focused on finding estimators that efficiently (i.e., using a small number of samples) obtain low errors with high probability.


3. **Complexity**: The "optimal" estimate, given a dataset, might be quite simple, or quite complex. We desire estimators that can flexibly adapt to the "best" complexity, given the context.  [*Semiparametric* estimators](https://www.amazon.com/Efficient-Adaptive-Estimation-Semiparametric-Models/dp/0387984739/ref=sr_1_6?s=books&ie=UTF8&qid=1537811338&sr=1-6&keywords=semiparametric) are the class of estimators that can be arbitrarily complex, but can achieve parametric rates of convergence. A maximally flexible estimator can obtain an arbitrarily complex estimate, while maintaining parametric rates of convergence.


5. **Stability**: Statisticians often quote George Box's quip "all models are wrong", which means that any assumptions that we make about our data (such as independence), are not exactly true in the real world.  Given that any theoretical claim one can make is under a set of assumptions, it is desirable to be able to show that an estimator's performance is *robust* to "perturbations" of those assumptions.  Such perturbations can include things like:
- data were assumed Gaussian, but the distribution has fat tails,
- there are a set of outliers, sampled from some other distribution,
- the model assumed two classes, but there were actually three, and,
- data were not sampled independently.
There are a number of [measures  of robustness](https://en.wikipedia.org/wiki/Robust_statistics#Measures_of_robustness), including breakdown point and influence function.

7. **Scalability**: If an estimator requires an exponential amount of storage or computation, as a function of sample size or dimensionality, than it can typically only be applied to extremely small datasets.  Similarly, if the data are relatively large (meaning that it takes "a while" to estimate), than estimation can often be sped up by a parallel implementation.  However, the "parallelizability" of estimators can vary greatly.  The theoretical complexity of an estimator is typically written as a function of sample size,  dimensionality (either intrinsic or extrinsic), and number of parallel threads.

9. **Explainability**: In many applications, including scientific inquiries, medical practice, and law, explainability and/or interpretability are crucial.  While neither of these terms have accepted definitions, certain general guidelines have been established.  First, the fewer features the estimator uses, the more interpretable it is.  Second, passing all the features through a complex data transformation, such as a kernel, is relatively not interpretable.  Third, when using decision trees, shallower trees are more interpretable, and when using decision forests, fewer trees are more interpretable.  In that sense, in certain contexts, relative explainability can be explicitly quantified.

7. **Automaticity**: In most complex settings, estimators have a number of parameters themselves.  For example, when running k-means, one must choose the number of clusters, and when running PCA, one must choose the number of dimensions. The ease and expediency with which one can choose/learn "good" parameter values for a given setting is crucial in real data applications.  When the most computationally expensive "process" of an estimator can be nested, leveraging the answer from past values, this can greatly accelerate the acquisition of "good" results.  For example, in PCA, by projecting into $D$ dimensions, larger than you think you'll need, you have also projected into $1, \ldots, D-1$ dimensions, and therefore do not need to explicitly compute each of those projections again. This is in contrast to non-negative matrix factorization, where the resulting matrices are not nested. This desiderata is the least formal of the bunch, and most difficult to quantify.