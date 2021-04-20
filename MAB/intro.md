# (G) Model: Multi-armed bandits

The setting is the following: there are $K$ machines, which are each given by a distribution on rewards in $[0,1]$.
Initially, we do not know anything except for the number $K$ of machines.
At each time step, we can choose a machine $i$ and get a reward from $i$ drawn from the distribution attached to this machine. 
The goal is to **maximise the expected total payoff**, as a function of the number of steps.
An algorithm selects at each time step a machine $i$ based on the choices made and rewards observed so far. 
We write (for the sake of clarity we will not mark the dependence on the algorithm on each random variables):
* $i$ is typically a machine (in $[1,K]$)
* $\mu_i$ is the expectation of the distribution for the machine $i$
* $t,T$ are number of steps
* $n(i,T)$ is the number of times the machine $i$ was played in the first $T$ steps
* $M(t)$ is the machine chosen at time step $t$
* $X(t)$ is the reward from the machine chosen at time step $t$
* $X(i,t)$ is the reward of playing the machine $i$ at time step $t$; if $i$ was not chosen at time $t$, then it is $0$
* $\widehat{X}(i,T)$ is the **empirical** reward of machine $i$ in the first $T$ steps, defined by

$$\widehat{X}(i,T) = \frac{1}{n(i,T)} \sum_{t \in [1,T]} X(i,t)$$

An optimal machine is one maximising the average reward $\mu$. 
We write `*` for this machine, so for instance its average reward is $\mu_{*}$.
The goal is to maximise

$$
\E [ \sum_{t \in [1,T]} X(t) ]
$$
