# (G) Regret

We are interested in defining a metric quantifying the quality of one algorithm.
The first quantity that comes to mind should be:

$$
\E [ \sum_{t \in [1,T]} X(t) ]
$$

this quantity enables to compare between two algorithms, the better one having a better total expected payoff.
However, it lacks any hint as to how optimal this algorithm is.
In other words, it does not relate to the optimality of the choices made by the algorithm.

Instead, we can define the regret of an algorithm 
as the difference between the optimal reward in $T$ steps (which is simply $\mu_* \cdot T$) and the obtained reward:

$$
R = \mu_* \cdot T - \sum_{t \in [1,T]} X(t)
$$

Maximising the expected total payoff is the same as minimising the regret, thus an optimal algorithm has null regret.
Note that both are functions of $T$ the total number of steps, hence it is not clear what it means to minimise or maximise.
We may consider the asymptotic behaviour, but of course it is more desirable to also give bounds for any $T$.
