# Newton's method in multiple dimensions

In this post I'll go over Newton's method in multiple dimensions. I continue assuming that you've read the previous post.

You can use Newton's method both for root finding and for optimization (finding a minimum or maximum). In this post I'll use a running example related to root finding. It might be that you're not interested in root finding, but rather in finding an extremum (minimum or maximum) of some multivariate function. As mentioned in the previous post root finding and extremum finding ideas are actually very similar. The extremum of a function is at the same point as where all gradients are zero. So finding an extremum of function $F(x,y)$ is the same as finding the roots of _all_ the gradients of that function $$\begin{bmatrix}\frac{\partial F(x,y)}{\partial x} \\ \frac{\partial F(x,y)}{\partial y}\end{bmatrix} = \begin{bmatrix}0 \\ 0\end{bmatrix}$$
So in the remainder of this post I'll talk about root finding, but I hope it is now clear that the ideas still translate to extremum finding.

## Example
In the remainder of this post I'll assume you're working with two equations and two variables. To find a solution, you need as many equations as variables. At the end of this post I'll explain what happens if this is not the case.

Online you can find many very generalized versions of Newton's method for an undefined number of dimensions, but for visualization purposes I'll keep stick to two.

So imagine you have the following functions
$$\begin{align}f(x,y) = xy + x^2 - 1 \\g(x, y) = \sin(xy)\end{align}$$

![Two plots containing f(x) and g(x)](/images/newtons_method/newton_2d.png)

The black contours indicate where the functions are zero.

You can use Newton's method to find the values of $x$ and $y$ that make sure that $f(x,y)$ and $g(x,y)$ zero, simultaneously.

$$\begin{bmatrix}x_{n+1} \\y_{n+1} \end{bmatrix} = \begin{bmatrix}x_n \\ y_n \end{bmatrix} - J^{-1}\begin{bmatrix}f(x_n,y_n) \\ g(x_n, y_n) \end{bmatrix}$$
where $J^{-1}$ is the _inverse_ of the _Jacobian_ of the vector $\begin{bmatrix}f(x_n,y_n)\\ g(x_n, y_n)\end{bmatrix}$

To compute the Jacobian, for each row, we compute the derivative over all variables, and place these in the columns.

In our case this means we get
$$J = \begin{bmatrix}\frac{\partial f}{\partial x} & \frac{\partial f}{\partial y} \\ \frac{\partial g}{\partial x} & \frac{\partial g}{\partial y} \end{bmatrix} =\begin{bmatrix}y+2x & x \\y\cos(xy) & x\cos(xy)\end{bmatrix}$$

## Solving our example

Below you can find a piece of python code that solves the problem
`
```
# make x on the domain -3 to 3
x = np.linspace(-3, 3, 100)
# make y on the domain -3 to 3
y = np.linspace(-3, 3, 100)

# make a function f(x,y) = xy + x^2 - 1
f_xy = lambda x, y: x*y + x**2 - 1

# make a function g(x, y) = sin(x*y)
g_xy = lambda x, y: np.sin(x*y)

dfdx = lambda x, y: y + 2*x
dfdy = lambda x, y: x
dgdx = lambda x, y: y*np.cos(x*y)
dgdy = lambda x, y: x*np.cos(x*y)

x_0 = 1
y_0 = 1

x_ns = [x_0]
y_ns = [y_0]

max_steps = 20
tolerance = 1e-4

results = {"step": [], "x_n": [], "y_n": [], "f(x_n, y_n)": [], "g(x_n, y_n)": []}

for step in range(max_steps):
    x_n = x_ns[-1]
    y_n = y_ns[-1]

    jacobian = np.array([[dfdx(x_n, y_n), dfdy(x_n, y_n)], [dgdx(x_n, y_n), dgdy(x_n, y_n)]])
    inv_jacobian = np.linalg.inv(jacobian)

    f_n = np.array([f_xy(x_n, y_n), g_xy(x_n, y_n)])
    delta = -inv_jacobian @ f_n

    x_ns.append(x_n + delta[0])
    y_ns.append(y_n + delta[1])

    results["step"].append(step)
    results["x_n"].append(x_n)
    results["y_n"].append(y_n)

    results["f(x_n, y_n)"].append(f_n[0])
    results["g(x_n, y_n)"].append(f_n[1])
    
    if np.linalg.norm(delta) < tolerance:
        break
```

and this quickly converges to the following solution

![Results of 2D newton](/images/newtons_method/2d_results.png)

## What if you have more variables than equations?
As I mentioned, you need as many equations as you have variables. But what if this isn't the case? Lets start with the case where you have more variables than equations. So for example you have the function
$$f(x,y) = xy + x^2 - 1$$
and you want to find the root. In this case the solution is simple: Just set one of the variables to any value, e.g. set $y=2$, and than solve Newton's method in the 1D-case (see the previous post).

## What if you have more equations than variables?
An alternative situation is that you have more equations than variables, e.g.

$$\begin{align}f_1(x) = x^2 - 1 \\ f_2(x)=-x\end{align}$$

![Plot of f_1(x) and f_2(x)](/images/newtons_method/overdetermined_newton.png)


How to find a value for $x$ such that both $f_1(x)=0$ and $f_2(x)=0$  simultaneously? 

Well actually, this is typically not possible, as is clear from the image above. It is not possible since the _system of equations is overdetermined_. If you're lucky it might be possible that both functions are $0$ at a given $x$, but this is not a given.





