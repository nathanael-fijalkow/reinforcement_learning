# (G) Regret

Equivalently, we can define the regret of an algorithm 
as the difference between the optimal reward in $T$ steps (which is simply $\mu_* \cdot T$) and the obtained reward:

$$
R = \mu_* \cdot T - \sum_{t \in [1,T]} X(t)
$$

Maximising the expected total payoff is the same as minimising the regret.
Note that both are functions of $T$ the total number of steps, hence it is not clear what it means to minimise or maximise.
We may consider the asymptotic behaviour, but of course it is more desirable to also give bounds for any $T$.
