---
title: "Introduction to the Monte Carlo with Python"
author: "Amjad Karim"
date: 2017-03-02
description: "Introduction to Monte Carlo methods, with a short
    practical example written in Python"
layout: post
---

## What is the Monte Carlo method?

Although it sounds exotic, the Monte Carlo method itself is actually
simple and elegant. It is a means of estimating the value of a variable
when the underlying function specifying it is unknown, unsolveable or
too complex. Crucially, the estimate improves as you increase the amount
of computing power, something we'll see later in this post.

Let's illustrate with an example, we'll use the Monte Carlo method to
estimate the value of the constant pi. Imagine we have a circle,
enclosed within a unit square. The ratio of the areas of the two shapes
provides a way of calculating pi. This illustration should help you see
what I mean.

![circle inside a square and how it helps estimate
Pi](/assets/sqcircle.png)

The Monte Carlo method can be used to estimate this ratio and therefore
pi .

1.  Draw a unit square on the floor and using a compass draw enscribe a circle sharing the same centre and with a diameter of 1m.
2.  Take a 100 small counters and drop them randomly onto the square
3.  Count the number of counters (X) which fell within the circle, the value X/100 is an estimate of the ratio of the two areas.
4.  Repeat steps 2 and 3 a large number (n) of times and take the average of the value calculated in step 3.
5.  Now pi is 4 divided by the average ratio.
6.  As you increase the number of repeats (n) or counters this calculation tends to the true value of pi.

Doing this manually quickly become tedious, and more importantly, is
limited by how fast someone would be able to repeat the trials.
Fortunately computers are quick and never get bored. This explains why
the Monte Carlo method took off after the advent of modern computers.
We'll continue and repeat this process using Python.

Before starting to write code, we need to introduce some elementary
geometry to help come up with a suitable algorithm.

## Equation for a Circle


The equation for a unit circle is the following:

$$x^2 + y^2 = 1$$

If the circle is contained in a 1×1 unit square and we randomly drop
points onto this square. Then a point is within the circle if:

$$y^2 < 1 - x^2$$

We can use this fact to determine whether any given point lies inside or
outside the circle. Let α denote the number of points which lie within
the circle and N denote the total number of points then as :math:\'lim\'
limN→∞

$$\frac{\alpha}{N} = \frac{\pi}{4}$$

Effectively this means we can get as close as we like to the correct
value by making N sufficiently large. In the following code example
we're going to increase the number of counters from 10 to 10,000,000 to
explore what happens to the Monte Carlo estimate for pi.

```python
import random as rd

def mc_pi(counters):
  inside_circle = 0 # tally the number of counters which land in the circle

  for i in range(0,counters):
      x = rd.random()
      y = rd.random() # generate a point with random (x,y)

      if y**2 <= 1 - x**2: #check the counter has landed in the circle
          inside_circle += 1

  estimated_pi = 4.*inside_circle/counters

  return estimated_pi
```

## How many iterations are required to converge to the true value of Pi?

We wrapped the code inside a reusable function so we could explore how
the number of counters effects the accuracy of the estimated value of
pi. We'll do this with the following code.

```python
"""code to display results of MC Pi Estimates."""

# import libraries
from math import pi
import numpy as np
import matplotlib.pyplot as plt

# generate the estimates and errors
ctrs = [10**i for i in np.arange(1, 8, 0.2)]
estimates = [mc_pi(int(i)) for i in ctrs]
errors = [abs(i - pi) for i in estimates]

# plot charts
plt.style.use('seaborn-deep')

# plot estimate chart
plt.subplot(2, 1, 1)
plt.plot(range(0, len(ctrs)), estimates, 'b+-')
plt.plot(range(0, len(ctrs)), [pi for i in estimates], '--')
plt.xticks([], [])
plt.title('Estimates of Pi')
plt.legend(['Estimates', 'Actual $pi])
plt.ylabel('Estimated Value')

# plot error chart
plt.subplot(2, 1, 2)
plt.plot(range(0, len(ctrs)), errors, 'r*-')
plt.xlabel('Log 10 Number of Counters')
plt.xticks(range(0, len(ctrs)), np.arange(1, 8.2, 0.2), size=8, rotation=90)
plt.xlabel('Log 10 Number of Counters')
plt.title('Absolute Errors')
plt.ylabel('Error')
plt.show()
```

Here using the function created earlier we visualise how the estimate
for pi improves with more counters.

We start at 10 and stop at 10,000,000. For me, the first estimate (with
10 counters) came out as 3.2, whereas the final estimate was
3.1418416972532563 (with 10,000,000). When you run the code your chart
will probably look different to mine that's because the estimates
generated with a small number of counters are volatile.

So we can see as the number of counters increase so does the accuracy of
the estimate. Unfortunately increasing accuracy doesn't come for free as
the more iterations we need, the greater computing resource (in terms of
either time or power) is required to complete the algorithm.

![increasing trials improves the
estimate](../img/pi_estimates.png)

Hopefully this post has helped you understand the Monte Carlo method
better as well as enabling you to implement it yourself. If you have any
questions or feedback please feel free to comment below.
