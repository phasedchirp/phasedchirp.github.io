---
layout: post
title: "Bayesian inference for estimating Net Promoter Score"
date: 2016-9-12
use_math: true
---

A while ago for a job interview I was asked to give estimate [Net Promoter Score]() with 95% confidence intervals for some user survey data. I'd never had to calculate confidence intervals for this particular form of response before, so I searched a bit and found a range of answers. One option was using an [analytic approximation](http://stats.stackexchange.com/questions/18603) which had the advantage of being fast and efficient at the cost of making some distributional assumptions that I wasn't thrilled with. In addition, I wanted the option doing some smoothing on the counts because, in the particular data set I had, there were several nearly-empty bins (in particular, the survey responses included )

So I decided to try implementing my own probabilistic model using [Stan](http://mc-stan.org/) so I could make my own potentially dubious distributional assumptions and then fit the model using Markov Chain Monte Carlo (MCMC) sampling. In particular, it let me specify a prior distribution which I felt was helpful given the data sparsity in the responses I was working with (for some reason, there were a few response values which were almost entirely unused). Here I used a Dirichlet prior (equivalent to add-one smoothing on the counts).

My first pass just treated responses as having a multinomial distribution:
{% gist a99768c788c8f37974b3852072bda42a %}

Confidence/credible intervals are then estimated using posterior predictive simulations (the `generated quantities` block in the code). By modeling the responses as conditional on some set of demographic variables or other information, it's also possible to compare response patterns for different groups of interest. This is convenient and easy to work with, but ignores the fact that, for example, a 1 and a 2 are more similar than a 1 and a 9. As currently implemented, it's also difficult to condition on continuous predictors.

As an alternative, I also implemented a version which used a cumulative logit model:
{% gist 5d1a6cc1b22a31d4ce8b0ab6f3a36953 %}

This was somewhat more complicated and slightly slower to converge, but takes into account the ordering of responses, while also incorporating smoothing. In addition, with this particular parameterization, it becomes somewhat easier to add continuous predictors (e.g. account age).


As an alternative to the above, many sources also recommend bootstrapping, although this still doesn't address ordering of response categories or the data sparsity issues. But, combined with manual smoothing (simply adding some amount of pseudo-data to each response count), it would allow me to avoid making the distributional assumptions that I made with the parametric models here. In terms of return on effort, all of the approaches, including the analytic approximation, give quite similar results, so if you just want a quick estimate, the time required for fitting a model with MCMC might not be worth it. However, having the MCMC samples to approximate the full posterior predictive distribution also allows generation of other quantities as functions of those statistics, like estimated returns or turnover rate.
