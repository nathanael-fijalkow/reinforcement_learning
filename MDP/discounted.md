# (G) Discounted case

The discounted payoff is defined by

$$
\sum_{t \ge 1} \gamma^t R_t
$$

for a fixed discount factor $\gamma$ less than $1$.

Recall that $\val_*(s) = \sup_{\sigma \text{ strategy}} \val_\sigma(s)$.
The goal is to construct an optimal strategy $$\sigma,$$ i.e. maximising the expected discounted payoff:

$$
\val_\sigma(s_0) = \E_{\sigma,s_0} [ \sum_{t \ge 1} \gamma^t R_t ] = \val_*(s_0)
$$
