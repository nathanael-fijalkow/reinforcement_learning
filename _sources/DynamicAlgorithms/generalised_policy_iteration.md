# The generalised policy iteration algorithm

The generalised policy iteration algorithm simply alternates between evaluation and improvements steps, possibly not performing perfect evaluations.

Let us get back to the value iteration algorithm, and observe that in some sense it also consists in alternating evaluation and improvement steps.
At every step, we have a value function $v_n$ and the algorithm computes $v_{n+1} = L(v_n)$ defined by

$$
v_{n+1}(s) = \max_{a \in A} q_{v_n}(s,a).
$$

We can interpret this as doing the following two steps in one go:
* from $$v_n$$ compute the $q$-value $q_{v_n}(s,a)$ through one step of the evaluation task
* construct the value function $v_{n+1}$ induced by $q_{v_n}$ through the improvement task

The main difference with policy iteration is that $v_n$ does not represent the values of a given strategy.

