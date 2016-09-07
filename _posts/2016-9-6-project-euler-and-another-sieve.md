---
layout: post
title: "Project Euler problem 35 and a faster prime sieve"
date: 2016-9-6
use_math: true
---

This blog has been rather neglected, but I'm getting towards the end of my batch at the [Recurse Center](https://www.recurse.com) in New York so I'm going to try updating it again.

My [previous post](http://phasedchirp.github.io/2015/10/31/functional-prime-sieve.html) showed a code-golfed prime sieve I wrote for fun in Haskell.

{% highlight Haskell %}
-- generate a list of prime numbers less than or equal to n
import Data.List
primeList :: Integral a => a -> [a]
primeList n = nubBy (\x y-> x`mod`y==0) [2..n]
{% endhighlight %}

While trying to solve [problem 35](https://projecteuler.net/problem=35) from Project Euler ("How many circular primes are there under 1,000,000"), I found myself needing something like this again. Unfortunately, `nubBy` has quite bad performance in this situation, so the one-line version is really inefficient. But, using `Data.Vector` there turns out to be a much faster option:

{% highlight Haskell %}
import qualified Data.Vector as V (Vector(..),fromList,toList, (//), (!), foldl')

candidates :: Int -> V.Vector Int
candidates n = (V.fromList [2..n]) V.// filters
  where filters = concat $ map (\x -> isMult x n) [2..n]

isMult :: Int -> Int -> [(Int,Int)]
isMult x u = [(x*m-2,0) | m <- [2..(u `div` x)]]
{% endhighlight %}

This version takes the vector of numbers `[2..1000000]` and sets all element that are multiples of any other number in the vector to 0, taking advantage of the fact that Vector has $O(1)$ indexing, rather than $O(n)$ for lists, and `Data.Vector`'s batch update operator `//` so updates can be staged and done all at once rather than done individually. This is not surprisingly much faster than recursively filtering a list as is done with `nubBy`. There's still some redundancy in how `isMult` generates updates, plus some less-than-ideal fiddling with indexes, so there's a lot of space for improvement, but using this instead of the [list-based version](https://gist.github.com/phasedchirp/dc3e3841c4f035bb304f289a6837ff07) took the [full solution](https://gist.github.com/phasedchirp/5d3fb6957dd397c80e3782d0b4a2f86e) from unusably slow to running in ~7 seconds.
