---
layout: post
title: "TITLE"
date: 2015-11-X
---

Continuing a theme from previous posts, this is a bit more messing around in Haskell. Specifically, to keep myself from getting too rusty, this is part of a first go at writing a linear algebra library.

###Representation:

First step is defining a matrix type. I'm using a lists of lists of **Num** as a starting point. With this type of matrix represetation, there are [two options](https://en.wikipedia.org/wiki/Row-major_order) as far as represetation of rows and columns and I'm choosing to go with column-major order. Adding a field storing shape information, here's a first attempt:

{% highlight Haskell %}
data Matrix = Mat{
        shape:: (Integer, Integer),
        val:: [[Num]]
    }
{% endhighlight %}


Defining a **Show** instance for the type, along with a utility function to swap things around into a convenient order for printing (from [here](http://stackoverflow.com/questions/2578930)):

{% highlight Haskell %}
t :: [[a]] -> [[a]]
t ([]:_) = []
t x = (map head x): t (map tail x)

instance Show Matrix where
    show (Mat x z) = intercalate "\n" (map show $ t z)
{% endhighlight %}

The function used to reorganize things for printing also does most of the work needed for transposing:

{% highlight Haskell %}
transpose :: Matrix -> Matrix
transpose (Mat (x,y) z) = Mat (y,x) (t z)
{% endhighlight %}

Next up is defining operations on matrixes:

####Addition:

{% highlight Haskell %}
matPlus :: Matrix -> Matrix -> Matrix
Mat w x `matPlus` Mat y z = 
{% endhighlight %}

####Multiplication:
Matrix multiplication is a bit more involved. First, defining a function **dot**:
{% highlight Haskell %}
dot :: [Double] -> [Double] -> Double
dot x y = sum $ zipWith (*) x y
{% endhighlight %}

This gives the standard dot product for two vectors $x$ and $y$ as $x \cdot y = \sum_1^n x_i*y_i$



###Making the Matrix type a member of the **Num** typeclass?
A minimal complete definition for an instance of **Num** requires +, *, -, abs, signum, and fromInteger. Most of these are possible, although this is going to require some stretching of definitions...

The addition and multiplication functions defined above give + and * already. Absolute value and signum are a bit harder