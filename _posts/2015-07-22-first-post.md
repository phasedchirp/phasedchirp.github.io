---
layout: post
title: "First blog post"
date: 2015-7-22
---

I have an exciting new website which is still under construction. I'm not sure how much I'm going to be updating this blog, but for a first post, here's a Haskell program that generates exponentially distributed random numbers using [inverse transform sampling](https://en.wikipedia.org/wiki/Inverse_transform_sampling). At the moment, you'll need to pipe the output somewhere useful since it doesn't do output files.

{% highlight haskell %}
-- takes arguments rate and number of samples
import System.IO
import System.Environment
import System.Random

main = do
    (l:x:_) <- getArgs
    gen <- getStdGen
    let n = read x :: Int
        rate = read l :: Float
        rs = randUnif gen n
        es = map (expQuant rate) rs
    mapM_ putStrLn $ map show es

-- uniform random numbers in the interval (0,1)
randUnif :: (RandomGen g) => g -> Int -> [Float]
randUnif g n = take n $ randomRs (0,1) g

-- quantile/inverse CDF function for an exponential distribution with rate r
-- maps [0,1] to the range of the function
expQuant :: Float -> Float -> Float
expQuant r x = - (log (1-x))/r
{% endhighlight %}
