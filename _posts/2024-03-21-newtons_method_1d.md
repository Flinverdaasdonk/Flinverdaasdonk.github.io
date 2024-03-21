# A simple introduction to Newton's method

Newton's method, also known as Newton-Raphson, is a technique used two ways:
- Root finding of an equation, or a system of equations
	- Root finding means finding the points where an equation $f(x) = 0$
- Finding the extremum (minimum or maximum) of a (possibly multivariate) equation.

If you know a little bit about calculus, you can understand why the method can be applied for both problems. The extremum of function $f(x)$ is the point where the gradient $f'(x) = 0$. So If you want to find the extremum of $f(x)$, you can simply look for the root of the gradient $f'(x)$.

Because it will be confusing to talk about both applications simultaneously I will only talk about the root-finding application in this post. However, if you are interested in finding the extremum of your function, simply take the first derivative of the function that you're trying to optimize, after which you can look for the root of that function. 

## Intuitive understanding of Newton's method 
Newton's method is an _iterative_ method to update your current best guess of the root.
Imagine you have a function $f(x_n)$ and are able to compute the first derivative $f'(x_n)$. In that case you can find the root using Newton's method by iteratively applying
$$x_{n+1}= x_n - \frac{f(x_n)}{f'(x_n)}$$
where $n$ indicates how many times yo have applied the method.

The first time I saw this format I was a little bit surprised. I could understand why the slope  $f'(x_n)$ is useful, but why is it in the denominator? And why is $f(x_n)$ in the numerator?
Here are a couple ways that guided my intuition
- If you're very far away from zero, i.e. if $f(x_n) >> 0$ it is okay to take a big step to update your method. If you're already very close to zero, i.e. $f(x_n) \approx 0$ you want to take a very small step. This is why you should scale your update with $f(x_n)$, and that's why it is in the numerator 
	- The same reasoning holds in case if $f(x_n) << 0$, after adjusting for the sign.
- If the slope $f'(x_n) >> 0$ it is very steep, and you're going to zero quickly. Therefore, you don't want to update your guess $x_n$ by moving along with this slope too much. By placing the slope in the denominator, you're adjusting your update size with the slope: If the slope is very steep you're quickly going to zero, so you want to take a small step. If the slope is very slanted, you're slowly going towards zero, so it is okay to take a larger step.
- The ratio $f(x_n)/f'(x_n)$ tells you how far to move along the x-axis at $f(x_n)$ with slope $f'(x_n)$ to reach to zero point. To if $f(x_n)=2$ and $f'(x_n)=\frac{1}{2}$, we need to move _back_ with $\Delta x=4$ to reach zero.

## Mathematical argument for the method
Although the previous section helped my intuitive understanding it is hand-wavy. A more rigorous approach often used to support the method is based on the Taylor expansion.

Recall the Taylor expansion of $f(x)$ evaluated around $x_0$:

$$f(x) = f(x_0) + \frac{f'(x_0)}{1!}(x-x_0) + \frac{f''(x_0)}{2!}(x-x_0)^2 + ... = \sum_{n=0}^\infty\frac{fn(x_0)}{n!}(x-x_0)^n$$
Based on this we can take the first order Taylor approximation
$$f(x) \approx f(x_0) + \frac{f'(x_0)}{1!}(x-x_0)$$
$$f(x) \approx f(x_0) + f'(x_0)(x-x_0)$$
Now if we want to find the root of $f(x)$ we can say that

$$f(x) = 0 \approx f(x_0) + f'(x_0)(x-x_0)$$
$$0 \approx f(x_0) + f'(x_0)x-f'(x_0)x_0$$
$$-f'(x_0)x \approx f(x_0) -f'(x_0)x_0$$
$$x \approx \frac{f(x_0)}{-f'(x_0)} - \frac{f'(x_0)x_0}{-f'(x_0)}$$
$$x \approx -\frac{f(x_0)}{f'(x_0)} - -x_0$$
$$x \approx x_0 -\frac{f(x_0)}{f'(x_0)}$$
which is Newton's method

## An example
Imagine we have the function
$$f(x)= \sin(2x^2) + 2x^2 - 2x - 10$$
![C:\Users\VerdaasdonkF\OneDrive - University of Twente\Documents\00_PhD\04_Programming\newton_raphson\newton_method_1D.png](file:///c%3A/Users/VerdaasdonkF/OneDrive%20-%20University%20of%20Twente/Documents/00_PhD/04_Programming/newton_raphson/newton_method_1D.png)

For this function we can analytically compute the gradient
$$f'(x) = \frac{df}{dx}= 4x\cos(2x^2)+4x-2$$

(as a sidenote: Newton's method also works if you use an approximate gradient at every required point using a finite difference method.)

Based on $f(x) f'(x)$ we can define the Newton step
$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)} =x_n - \frac{\sin(2x^2) + 2x^2 - 2x - 10}{4x\cos(2x^2)+4x-2}$$


## Solving the example
Below is a piece of python code I wrote to solve the problem

<pre> ```
# define x in the range from -2 to 2

xs = np.linspace(-3, 3, 100)

# define f(x) = sin(2x^2) + 2x^2 - 2x - 10
fx = lambda x: np.sin(2*x**2) + 2*x**2 - 2*x -10
dfdx = lambda x: 4*x*np.cos(2*x**2) + 4*x - 2

x_ns = [-0.1]  # initialize our guesses
tol = 1e-4  # tolerance between steps; if the improvement is less than this, we'll stop iterating
max_steps = 20  # if we haven't reached the tolerance within this many steps, we'll stop
results = {"steps": [], "x_ns": [], "fx": [], "dfdx": []}

for step in range(max_steps):
    _fx = fx(x_ns[-1])
    _dfdx = dfdx(x_ns[-1])


    results["steps"].append(step)
    results["x_ns"].append(x_ns[-1])
    results["fx"].append(_fx)
    results["dfdx"].append(_dfdx)

    x_n = x_ns[-1] - _fx/_dfdx
    x_ns.append(x_n)

    if abs(x_ns[-2] - x_ns[-1]) <= tol:
        # stop iterating once we have passed our tolerance
        break
``` </pre>


You can that the method very quickly finds a good root of the function.

In the next post I explore Newton's method in multiple dimensions