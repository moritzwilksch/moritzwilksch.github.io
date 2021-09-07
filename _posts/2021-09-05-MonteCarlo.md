---
layout: post
title:  "Monte Carlo Simulations: Math for Lazy People"
date:   2021-09-05 22:45 +0200
tags: monte-carlo simulation
---
## Monte Carlo Simulations
Inspired by the gambling paradise Monte Carlo in Monaco, Monte Carlo simulations use repeated random sampling to numerically solve problems that are otherwise hard or impossible to solve. For probabilistic problems, for example, we can use a Monte Carlo simulation to simulate a complex process millions of times and record its outcome. An approximate probability distribution of the outcome can then be obtained by simply examining the relative frequencies of the observed outcomes.  
In this post, we will solve not an impossible, but a surprisingly hard geometrical problem using Monte Carlo simulations: Calculating the area of intersection between two circles.

## Intersecting Circles
Two circles in 2-dimensional space can be placed in space in three different ways to each other: Non-overlapping, partially overlapping or one circle completely contained in the other. For the first and last case, the area of intersection is easy to calculate:
- **Non-overlapping**: The area of intersection is 0.
- **One contained in another**: The area of intersection is the area of the smaller circle.  

In the case of partially overlapping circles, however, the math becomes a bit more involved. Take for example two overlapping circles with the following characteristics:  


| |**Circle 1 (Red)**|**Circle 2 (Blue)**|
|**Center**|$$(-2, -1)$$|$$(3, 3)$$|
|**Radius**|$$3$$|$$6$$|




|![Overlapping circles](/assets/images/montecarlo/overlapping-circles.png)|
|Two partially overlapping circles of different sizes.|  


As it turns out, there is a formula for calculating this area, and it goes along the lines of the following:

$$A_{Intersection} = r_1^2 \cdot cos^{-1} \left(\frac{d_1}{r_1}\right) - d_1\sqrt{r_1^2 - d_1^2} + r_2^2 \cdot cos^{-1} \left(\frac{d_2}{r_2}\right) - d_2\sqrt{r_2^2 - d_2^2}$$  

Way more complex than I expected for this seemingly simple problem. So let's see how Monte Carlo simulations can save us some headache and deliver an approximate solution to this problem.

## The Simulation
The idea to solving this problem is that we can randomly generate points inside a rectangle that fully contains both circles. In this case, we'll be using the rectangle which is given by the plots' dimensions above: The plot stretches from -7 to 10 in the x-direction and from -5 to 10 in the y-direction. Thus, the size of the rectangle is 17 (along the x-axis) by 15 (along the y-axis). The area of this rectangle can be easily calculated as $$17 \cdot 15 = 255$$.  
If we now randomly scatter points across the rectangle, some of them will fall within the intersection between the two circles, and others won't. We need a mechanism to reliably calculate whether any given point falls into the intersection between the two circles. For this, we can use the circle equation. For any point $$P=(x, y)$$ it falls into a circle with center $$C=(cx, cy)$$ and radius $$r$$ if the following inequality is satisfied:  

$$(x - cx)^2 + (y - cy)^2 \leq r^2$$  

Or, in Python code:  

```python
def is_in_circ(x, y, cx, cy, r):
    return (x - cx)**2 + (y - cy)**2 <= r**2
```

Moreover, a point falls in the intersection between the two circles, if it simultaneously is in circle 1 *and* circle 2. We can now use a random generator to generate *uniformly distributed* points inside the rectangle that contains our two circles.  

```python
import numpy as np
N_POINTS = 1000
xs = np.random.uniform(-7, 10, N_POINTS)
ys = np.random.uniform(-5, 10, N_POINTS)
```

Finally, we have to check the percentage of points that actually fall into the intersection. We can do that by using an element-wise and (&) to ensure a point falls into the intersection and calculate the mean of this boolean array to express this number as a percentage

```python
in_intersection = (
    is_in_circ(xs, ys, -2, -1, 3)  # circle 1
    &
    is_in_circ(xs, ys, 3, 3, 6)  # circle 2
)

in_intersection.mean()
```

The figure below shows how the process looks visually with only a few thousand points. The points that fall into the intersection of the two circles are highlighted in yellow. Once we obtain the percentage estimate that p% of points fall into the intersection, we can simply calculate p% of the entire rectangle area and have an estimate of the actual area of intersection.

|![Monte Carlo simulation for the area of intersection](/assets/images/montecarlo/mc-simulation.gif)|
|Monte Carlo simulation with different numbers of points.|  

As you might notice, the percentage of points inside the intersection oscillates quite a bit in the beginning but stabilizes the more points you use. Finally, it settles around $$4.15\%$$. We can use this number to approximate the intersections area as: $$0.0415 \cdot 17 \cdot 15 = 10.5825$$. Using the actual formula (which you can do using [this online calculator](http://www.ambrsoft.com/TrigoCalc/Circles2/circle2intersection/CircleCircleIntersection.htm)) yields an actual area of $$10.328$$, so our approximation is pretty close!  

Just to take another look at the stability of our estimate and gauge how many points we actually need for a reliable estimate, let's plot the area of intersection against the number of points used to obtain the estimate:

|![Stability of estimate](/assets/images/montecarlo/stability-estimate.png)|
|Estimated % of points in the intersection for different number of total points|  

Looks like even using just 40,000 points might be enough to gain a good estimate. More points should always give a more reliable estimate, but the returns of adding more points diminish rather quickly.

## Conclusion
This post demonstrates how to use a Monte Carlo simulation instead of an analytical approach to solve a mathematical problem. These simulations come in handy especially for probabilistic problems, but as we've seen can also be applied to other types of problem settings. Monte Carlo simulations are not a silver bullet, though: We only work with approximations here, which can be tedious for very small numbers. They also require quite a lot of computational effort compared to an analytical solution. However, I'd argue that they are great for using your intuition to find roughly correct solutions for complex problems.