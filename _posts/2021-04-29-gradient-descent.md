---
layout: post
title:  "Introduction to Gradient Descent"
date:   2021-04-29 09:45 +0200
tags: machine-learning optimization gradient-desent
---

One paramount principle underlying many, if not most, of the machine learning algorithms applied in practice is Gradient Descent. But how does this optimization technique work under the hood? Let’s figure out the intuition, the math, and the code that goes into this algorithm.

## What do we need it for?
Gradient descent is an optimization algorithm. Thus, it is used to find the minima or maxima of a function. This comes in handy in machine learning. Generally, in supervised machine learning we always need:
1. Training data
2. A model which maps input data to some output value 
3. Parameters inside this model we can alter to change this mapping
4. A function that quantifies how well our current model fits the training data (“loss function”)  

The overarching goal is to choose parameter values such that the model fits the data as well as possible, (i.e. minimize the loss function). Now, we could randomly try out parameter values, evaluate the goodness of fit of the model (+ rinse and repeat) but there is a better approach: We can use gradient descent to find a minimum of the loss function.  
**NOTE**: while everything described in this post can be used to find either maxima or minima, it is more common to construct a loss function such that its minimum is the optimal value. Hence, we’ll just look at minima from here.

## Wait, I already know how to find minima …
Finding minima used to be easy back in school: Given a function f, we find its local extrema by taking its derivative, setting it zero, and solve for x. Let’s take for example:  

$$ 
f(x) = \frac{2}{3}x^3 + 3x^2 +4x+2
$$  

Taking its derivative yields:  

$$ \frac{df}{dx} = f’(x) = 2x^2 + 6x + 4 $$  

Setting this zero and solving results in:  

$$ f’(x)=0 \Leftrightarrow x = -2 \vee x=-1$$  


Therefore, $$f$$ has a local extremum at both of these x-values. (Note: One of these extrema is a maximum. This can be verified by using the second derivative, but is not too relevant for now).  

## *Why* did that even work? 
As it turns out, the first derivative of a function models its *slope*. The slope of a function at a specific point can be visually represented as the slope of the *tangent line* at that specific point. The plot below shows the function $$f$$ and two tangent lines, one at $$x=-2$$ (red) and one at $$x=-0.5$$ (green). Their slopes are $$0$$ and $$1.5$$ respectively. Now we see why minima and maxima always coincide with points at which $$f$$ has a slope of zero.

|![Two Tangent Lines](/assets/images/gradient-descent/two_tangents.png)|
|Two tangent lines of f. Tangents at local extrema always have a slope of zero.|

Taking the approach outline above is called finding an **analytical solution**. That is, there is a specific list of logical steps you can follow to obtain an exact result. Unfortunately, some problems, especially in machine learning, do not have an analytical solution (or even if they have, it's computationally expensive to calculate them). How do we proceed in this case?


## Gradient Descent to the Rescue!
Gradient descent is a **numerical approach** to solving the same problem outlined above. This means that there is no single formula that we can solve to obtain an exact result, but instead, we approximate the real solution. Even if the approximation might not be perfectly the same as the analytical solution, it’s more often than not “good enough”. Usually, we do so by (naively or intelligently) trying out different values and seeing whether they constitute a “good” solution or not.
The gradient descent algorithm works as follows:
1.	Start at a random value of x.
2.	Take a small step in **the direction of the minimum**
3.	Repeat 1 & 2 until you find the minimum

## The “Direction of the Minimum”
To find the direction in which we have to step, we can once again use the first derivative of the function $$f$$. We can see in the plot above that when the slope of $$f$$ is positive, we have to step to the left. Consequently, when the slope of $$f$$ is negative, we have to step to the right. Generally, **we always step in the opposite direction of the slope**. Formally:  

$$x_{new} = - \gamma \cdot f’(x_{current})$$

Where $$ x_{new}$$ is the x-value after the step, $$ x_{current}$$ is the current x-value and $$\gamma$$ is a small number (say, 0.1) that slows down the whole process to prevent “overshooting” the minimum. If all works well, the process should look something like this:

|![Gradient Descent](/assets/images/gradient-descent/gradient_descent.gif)|
|Gradient Descent, with $$\gamma = 0.1$$|

If $$\gamma$$ is chosen too large, the process *diverges*, which means it’s not incrementally approaching the right solution, but jumping around like crazy:

|![Diverging Gradient Descent](/assets/images/gradient-descent/diverging_gradient_descent.gif)|
|Diverging Gradient Descent, with $$\gamma = 0.7$$|

## "Derivative" Descent?
Now, we did not even mention the word *gradient* once until now. Why is that? The dummy function in this example is *univariate*: Its output value only depends on one single input variable. In reality, we'd work with functions that depend on multiple input variables and are additionally parameterized by thousands or millions of parameters. This is where the *gradient* comes in.  
The *gradient* is simply a vector that subsumes all possible partial derivatives of a multivariate function. Let' see an example:

Let 

$$f(a, b) = a^4 - 2ab + b^2 - 8$$

its partial derivates (separately deriving with respect to a, and then b) are:

$$\frac{\partial f(x)}{ \partial a} = 4a^3 - 2b$$

and

$$\frac{\partial f(x)}{ \partial b} = -2a + 2b$$

Hence, the gradient of $$f$$, written as $$\nabla f(x)$$ would be:

$$\nabla f(x) 
= \begin{pmatrix} \frac{\partial f(x)}{ \partial a} \\ \frac{\partial f(x)}{ \partial b} \end{pmatrix} 
= \begin{pmatrix} 4a^3 - 2b \\ -2a + 2b \end{pmatrix} $$


So when working with multivariate functions, we'd use the gradient instead of just the slope to determine the (multidimensional) direction of steepest descent which hopefully brings us closer to the minimum.

## Outlook
There are some more details involved when using gradient descent for a real optimization problem. Gradient descent only finds local minima (unless the loss function is *convex*) and there are implementation details regarding how exactly the gradient is calculated. But for now, I hope you got a good intuition about how and why it works using a toy example.
