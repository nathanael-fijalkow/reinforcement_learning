# Policy iteration algorithm

This second algorithm manipulates only pure positional strategies. We define two tasks:
* **evaluation**: given a strategy $$\sigma$$, compute (or approximate) $$\val_\sigma$$
* **improvement**: given a strategy $$\sigma$$ and its (approximate) value function $$\val_\sigma$$, construct an improved strategy $$\sigma'$$

## The evaluation task

Given a strategy $$\sigma$$, its values satisfy the following equations

$$
\val_\sigma(s) = q_\sigma(s,\sigma(s)).
$$

One way of understanding this is that this forms a system of linear equations whose unknowns are $$\val_\sigma(s)$$ for each $$s \in S$$.
This can be solved using Gaussian elimination, which theoretically works but would be very ineffective.

A more practical solution is to use the previous fixed point algorithm specialised to the strategy $$\sigma$$:
we start from some vector $$v_0$$ and then construct better and better approximations:

$$
v_{n+1}(s) = q_{v_n}(s,\sigma(s)).
$$

For the same reasons as above, this process converges exponentially fast towards $$\val_\sigma$$.
We fix a threshold $$\varepsilon > 0$$ and stop when $$|v_{n+1} - v_n| \le \frac{\varepsilon}{2},$$
implying that $$|\val_\sigma - v_n| \le \varepsilon.$$

## The improvement task

Given a strategy $$\sigma$$ and its value function $$\val_\sigma$$ (we ignore the fact that we actually only compute an approximation!), 
we define 

$$
\sigma'(s) = \text{argmax}_{a \in A} q_{\sigma}(s,a)
$$

> **Lemma** We have $$\val_{\sigma'} \ge \val_{\sigma}$$.
Further, if for some $$s$$ we have $$q_{\sigma}(s,a) > \val_{\sigma}(s)$$,
then the inequality above is strict for $$s.$$

To see that the lemma holds, we write the equations for $$\val_\sigma$$ and $$\val_{\sigma'}$$ and compare them.

## The complete algorithm

The policy iteration algorithm is: 
starting from any strategy, apply alternatively evaluation and improvement to construct better and better strategies.
Note that since we consider only pure positional strategies, which are in finite number, and that each one strictly improves over the previous one,
then the algorithm terminates (that is, assuming we indeed compute the value function exactly, which we don't!).
When it does so, the last strategy $\sigma$ satisfies $$\val_\sigma(s) = \max_{a \in A} q_\sigma(s,a)$$,
which as we have seen above implies that it is optimal.

Why is it called **policy iteration**? Because it iterates on strategies (policies).
