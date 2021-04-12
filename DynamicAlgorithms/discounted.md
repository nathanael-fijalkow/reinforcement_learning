# The discounted reward

For the remainder of this post we consider the discounted payoff defined by

$$
\sum_{t \ge 1} \gamma^t R_t
$$

for a fixed discount factor $\gamma$ less than $1$.

Recall that $\val_*(s) = \sup_{\sigma \text{ strategy}} \val_\sigma(s)$.
The goal is to construct an optimal strategy $$\sigma,$$ i.e. maximising the expected discounted payoff:

$$
\val_\sigma(s_0) = \E_{\sigma,s_0} [ \sum_{t \ge 1} \gamma^t R_t ] = \val_*(s_0)
$$

We will present two algorithms:
* **value iteration**
* **policy iteration** (sometimes called **strategy iteration** or **strategy improvement**)

In the end we will argue that these algorithms both fall into a larger family of algorithms, called generalised policy iteration.
