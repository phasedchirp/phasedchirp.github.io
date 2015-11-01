---
layout: post
title: "A short prime sieve"
date: 2015-10-31
---

I'm currently in the process of job hunting, so to de-stress, I've been playing with Haskell a lot. Here's a very short function to generate lists of prime numbers.

{% highlight Haskell %}
-- generate a list of prime numbers less than or equal to n
import Data.List
primeList :: Integral a => a -> [a]
primeList n = nubBy (\x y-> x`mod`y==0) [2..n]
{% endhighlight %}

If instead, you want a list containing n primes:

{% highlight Haskell %}
-- generate a list of n prime numbers
--import Data.List
listPrimes :: Integral a => a -> [a]
listPrimes n = take n $ nubBy (\x y-> x`mod`y==0) [2..n]
{% endhighlight %}

Next project will hopefully be slightly more practical.