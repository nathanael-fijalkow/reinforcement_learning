# (T) Regret analysis of the UCB algorithm

We present a proof that the upper confidence bound yields an (asymptotically) optimal algorithm for regret minimisation of multi-armed bandits.

There is an accompanying github repository for experimenting with different algorithms for the [multi-armed bandits](https://github.com/nathanael-fijalkow/Multi-Armed-Bandits).

This result was proved by Auer, Cesa-Bianchi, and Fischer in [this paper](http://homes.di.unimi.it/cesa-bianchi/Pubblicazioni/ml-02.pdf). 
The proof is essentially the same (some calculations are a bit different).

## A class of algorithms: index policies

A very natural class of algorithms is to assign at each time step $t$ and to each machine $i$ a number $U(i,t)$, 
and to choose at each step the machine maximising this number.
(We first choose each machine once, to meaningfully initialise these numbers. We will not take this phase into account in the analysis later.)

The first naive attempt is to use $U(i,t) = \widehat{X}(i,t)$.
The empirical reward converges towards the actual expected reward, i.e. $\lim_T \widehat{X}(i,T) = \mu_i$,
but this does not imply that this algorithm finds the optimal machine in the limit.
Indeed, it may choose a machine once, get a bad reward from it, and decide never to play it again.
Consider for instance the case of two machines, the first one giving always reward $\frac{1}{2}$, and the second giving reward $0$ with probability $\frac{1}{4}$
and $1$ with probability $\frac{3}{4}$. The second machine is optimal since $\mu_1 = \frac{1}{2}$ and $\mu_2 = \frac{3}{4}$, but if we are unlucky and the first reward given by the machine $2$ is $0$,
then the machine $2$ will never be used again. This yields a regret which is linear in $T$.

The missing information is whether the empirical reward for machine $i$ can be considered **accurate or not**, in other words whether the machine $i$ was played enough times to trust the empirical reward.
This leads to indices of the form $U(i,t) = \widehat{X}(i,t) + c(i,t)$, with $c(i,t)$ reflecting on the quality of the empirical reward.

Another point of view is that using $U(i,t) = \widehat{X}(i,t)$ focusses on **exploitation** and does not reward **exploration**, which is precisely what $c(i,t)$ does.

It turns out that there is a choice of $c(i,t)$ which yields an algorithm with logarithmic regret:

> **Theorem:** (Auer, Cesa-Bianchi, and Fischer 2002)
The regret of the index policy for $c(i,t) = \sqrt{ \frac{\log(t)}{n(i,t)} }$ is $O(\log(T))$

This is asymptotically optimal as proved by Lai and Robbins in 1985.

## Why the choice of $c(i,t)$?

The role of $c(i,t)$ is to quantify the accuracy of the empirical reward.
Indeed, if the machine $i$ was not played a lot of times, its emprical reward may be off by a lot, 
and it is therefore interesting to choose it to increase the accuracy of the empirical reward, so $c(i,t)$ should be large in that case.

The Chernoff-Hoeffding bound gives some information on the accuracy of the empirical reward:

$$
\P(\widehat{X}(i,t) \ge \mu_i + c) \le \exp^{-2 c^2 n(i,t)}
$$

and similarly

$$
\P(\widehat{X}(i,t) \le \mu_i - c) \le \exp^{-2 c^2 n(i,t)}
$$

So, choosing $c(i,t) = \sqrt{ \frac{\log(t)}{n(i,t)} }$ implies an upper bound in $t^{-2}$, which we will see is exactly what we need.

## Proof of the logarithmic regret

We first observe that the regret $R$ is equal to

$$
\sum_{i \in [1,K]} (\mu_* - \mu_i) \cdot n(i,T)
$$

Indeed, $\E[\sum_{t \in [1,T]} X_t] = \sum_{i \in [1,K]} \mu_i \cdot n(i,T)$.

This means that it will be enough to upper bound $n(i,T)$ for all non-optimal machines $i$.

We let $\Delta_i$ denote $\mu_* - \mu_i$.

We fix a non-optimal machine $i$. 
Let $\ell$ to be determined later: since when the machine $i$ has been played only a few times, the empirical reward is very noisy,
we will be interested in studying the situation where it has been played at least $\ell$ times.
For an event $A$, we write $$\{A\}$$ for the random variable with value $1$ if $A$ is realised and $0$ otherwise. 

$$
n(i,T) \le \ell + \sum_{t \in [1,T]} \{M(t) = i \wedge n(i,t) \ge \ell\}
$$

By definition of the algorithm, for the machine $i$ to be chosen over the optimal machine $*$, we must have

$$
\widehat{X}_{*,t} + c(*,t) \le \widehat{X}_{i,t} + c(i,t)
$$

Let us denote by $A$ the event

$$
\widehat{X}_{*,t} + c(*,t) \le \widehat{X}_{i,t} + c(i,t)\ \wedge\ n(i,t) \ge \ell
$$

Let us define two events:
* $A_1 = \widehat{X}_{*,t} \le \mu_{*} - c(*,t)$, meaning that the optimal machine is underapproximated by $c(*,t)$
* $A_2 = \widehat{X}_{i,t} \ge \mu_i + c(i,t)$, meaning that $i$ is overapproximated by $c(i,t)$

We let $\ell = \frac{4 \log(T)}{\Delta_i^2}$, and show that this implies that
$A \subseteq A_1 \vee A_2$.

First, we have

$$
\mu_* \ge \mu_i + 2 c(i,t).
$$

Indeed, for $n(i,t) \ge \ell$, we have

$$
2c(i,t) = \sqrt{\frac{4 \log(t)}{n(i,t)}} \le \Delta_i = \mu_* - \mu_i.
$$

We prove the contrapositive. Assume that neither $A_1$ nor $A_2$ are realised, then we have
$\widehat{X}_{*,t} + c(*,t) > \mu_{*} \ge \mu_i + 2 c(i,t) > \widehat{X}_{i,t} + c(i,t)$,
meaning that $A$ is not realised.

It follows that the probability of $A$ is bounded from above by the sum of the probabilities of the two events $A_1$ and $A_2$.
The discussion above for the Chernoff-Hoeffding bound shows that they have each probability upper bounded by $t^{-2}$.

Thus

$$
n(i,T) \le \ell + \sum_{t \ge 1} 2 t^{-2} \le \frac{4 \log(T)}{\Delta_i^2} + \frac{\pi^2}{3}
$$

We can conclude: the regret is bounded by 

$$
\left( \sum_{i \in [1,K]} \frac{4}{\Delta_i^2} \right) \cdot \log(T) + \frac{\pi^2}{3} = O(\log(T))
$$

