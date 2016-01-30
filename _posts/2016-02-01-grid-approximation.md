---
layout: post
title: "Grid approximations with Haskell"
date: 2016-02-01
---

This post outlines a first attempt at using Haskell for Bayesian inference, using a grid approximation. To do this,


First step is implementing generating a grid of points. This is fairly straightforward with a list comprehension, but much more concise using the `<$>` and `<*>` operators from `Control.Applicative`

{% highlight Haskell %}
-- comprehension version:

-- applicative version:


{% endhighlight %}

Then, the probability density needs to be evaluated at each point:

{% highlight Haskell %}
-- generate a list of n prime numbers

{% endhighlight %}
