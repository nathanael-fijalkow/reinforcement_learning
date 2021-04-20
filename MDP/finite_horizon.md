# (G) Finite horizon case

We consider plays of length (exactly) $T$ a fixed finite value.

We let $\val_*(t,s)$ denote the optimal expected total reward from $s$ in $t$ moves.
The goal is to construct an optimal strategy $\sigma$, i.e. maximising the expected total payoff:

$$
\val_\sigma(T,s) = \E_\sigma[ \sum_{t = 1}^T R_t ] = \val_*(T,s)
$$

> **Lemma** $\val_*(0,s) = 0$ and 
$
\val_*(t,s) = \max_{a \in A} \sum_{s' \in S, r \in R} \Delta(s,a)(s',r) (r + \val_*(t-1,s'))
$

To see that this equation is correct, we remark that the value function for a (Markovian) strategy $\sigma$ satisfies the recursive equation:

$$
\val_\sigma(t,s) = \sum_{a \in A} \sigma(s)(a) \sum_{s' \in S,r \in \R} \Delta(s,a)(s',r) (r + \val_\sigma(t-1,s'))
$$

The key argument uses convexity.

The lemma in particular implies that there are optimal deterministic Markovian strategies. 
The dependence on the time step is necessary: closer to the time step $T$, it makes sense to play moves with higher immediate rewards but dire consequences,
while for a small $t$, moves should be selected to optimise not only the reward but also the states for their potential to yield higher rewards.

The equation can easily be turned into an algorithm (with pseudo-polynomial complexity), which computes $\val_*(t,s)$ for each $s$ for $t$ from $0$ to $T$. 
