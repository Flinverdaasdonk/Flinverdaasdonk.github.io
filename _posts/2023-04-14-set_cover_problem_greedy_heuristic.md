# Chvátal's greedy heuristic for the set cover problem

This post is divided in the following parts:
1. Brief description of the set cover problem
2. A greedy heuristic to solve the set cover problem

## Brief description of the set cover problem.

Consider we have a collection of points, and a collection of sets, where each set contains a subset of the collection of points and has an associated cost. 
Important is that all points are contained in at least one of the sets, and a point can be in multiple sets at once.
To illustrate, consider the figure below (source: [euroinformatica](http://www.euroinformatica.ro/documentation/programming/!!!Algorithms_CORMEN!!!/DDU0238.html).

[Figure of set cover problem](images/set cover problem.png)

Here you have sets $S_1$ through $S_6$, and the top-left point is in $S_1$ and $S_3$ simultaneously.

The set cover problem is as follows: Which sets are required to cover all points, at a minimum cost. In practice this turns out to be an NP complete problem.

For more details on the set cover problem, you can read the [wiki](https://en.wikipedia.org/wiki/Set_cover_problem)

## Chvátal's Greedy Heuristic for the set cover problem

Recall that the set cover problem is as follows: Which sets are required to cover all points, at a minimum cost. Chvátal proposed a classic greedy heuristic to solve the set cover problem in the paper [A Greedy Heuristic for the Set-Cover problem, 1979](https://www.jstor.org/stable/3689577)

Here is a description of the heuristic. As mentioned each set $S$ contains a number of points $n$, and has an associated cost $c$. 
The collection of all points together is called $N$. The heuristic is as follows:
0. Initialize a 'cover set' $J^*$. This will eventually contain all sets used to cover all points $N$.
1. Check if the sets in $J^*$ cover all points *N*. If yes: stop (you've found the cover). If no: continue to step 2
2. Find the set with the highest ratio of $n/c$. We'll call this set $k$
2. Put $k$ in $J^*$$
3. For all remaining sets, remove all points that also are $k$. You can do this since these points are already accounted for.
4. Go back to step 1.




