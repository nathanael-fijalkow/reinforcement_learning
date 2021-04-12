---
layout:     post
title:      Part 2, Markov decision processes and dynamic algorithms
date:       2019-11-27 9:00:00
author:     Nathana&euml;l Fijalkow
category:   Reinforcement learning
---

<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  TeX: {
    Macros: {
      R: "{\\mathbb{R}}",
      Q: "{\\mathbb{Q}}",
      N: "{\\mathbb{N}}",
      Z: "{\\mathbb{Z}}",
      M: "{\\mathcal{M}}",
      A: "{\\mathcal{A}}",
      B: "{\\mathcal{B}}",
      E: "{\\mathbb{E}}",
      P: "{\\mathbb{P}}",
      val: "{\\text{val}}",
      Dist: "{\\mathcal{D}}",
    }
  }
});
</script>

<p class="intro"><span class="dropcap">W</span>e present the model of MDPS, some fundamental properties and the two dynamic algorithms, value iteration and policy iteration.</p>

A Markov decision process is given by a set of states $S$, a set of actions $A$, and a transition function

$$
\Delta : S \times A \to \Dist(S \times \R)
$$

In words: from the state $s$ and playing action $a$, the outcome is drawn according to the distribution $\Delta(s,a)$, it yields a reward $r$ and leads to state $s'$ with probability $\Delta(s,a)(s',r)$.
Sometimes, the next-state and reward distributions are given independently, and actually in many cases the reward is deterministic and not stochastic.

We also specify an initial state $s_0$.

The objective of Markov decision processes is to model decision-making for a controller in a stochastic environment with immediate rewards.
Indeed, at every step a reward is observed (although in many scenarios we will consider the reward will be $0$ at each step except for the terminal step).

The distinctive properties of Markov decision processes is the **Markov property**: the distribution for the next state and the reward depend only on the **current** state and action, and not on the whole history. One commonly say "given the present, the future does not depend on the past", although I'm not sure this formulation helps.

A **play**, or **history**, is a sequence $$(s_0,a_0,r_0,s_1,a_1,r_1,\dots)$$, where $s_0$ is the first state, $a_0$ the first action, $r_0$ the first reward, and so on.

The decisions are represented by **strategies** (also called **policies**). There are five types:
* (general) strategies are functions $$\sigma : (S \times A \times \R)^* \times S \to \Dist(A)$$: given a history and the current state, the strategy chooses a distribution on actions
* **pure** strategies (also called **deterministic**) choose not a distribution on actions but a single action: $$\sigma : (S \times A \times \R)^* \times S \to A$
* **Markovian** strategies depend only on the current state and the time step: $$\sigma : \N \times S \to \Dist(A)$$ 
* **stationary** strategies (also called **positional** or **memoryless**) depend only on the current state: $$\sigma : S \to \Dist(A)$$
* **pure stationary** strategies: $$\sigma : S \to A$$.

It turns out that in most cases the simplest strategies will be enough for our purposes (we will prove this in due course).

An MDP $\M$ together with a strategy $\sigma$ induce a Markov chain, and in particular a probability measure $\P_\sigma$, or $\P_{\sigma,s_0}$ when specifying the initial state $s_0$.
(We will not go into the formal definitions of the Markov chain and the measure here.)
We let $S_i$ denote the random variable denoting the state, $A_i$ the action played, and $R_i$ the reward obtained at the $i$-th step.

Given a strategy $\sigma$ we write $\val_\sigma$ for the value function defined by

$$
\val_\sigma(s) = \E_{\sigma,s} [\sum_i R_i],
$$

meaning the total expected reward obtained while playing $\sigma$ and starting from $s$.
The sum is a priori infinite, hence may not exist in general. 
There are two different solutions to that: 
* we fix some horizon and only look at paths of length the fixed horizon,
* we introduce a discount factor $\gamma \in (0,1)$ we consider the total expected discounted reward:

$$
\E_\sigma [\sum_i \gamma^i R_i]
$$

Since $$\gamma$$ is less than $$1$$, the rewards collected at the beginning are more important than the rewards in a more distant future.

The (algorithmic) goal in **reinforcement learning** is to find a strategy maximising the total expected reward:

$$
\val_*(s_0) = \sup_{\sigma \text{ strategy}} \val_\sigma(s_0)
$$

## Dynamic algorithms

Dynamic algorithms make two (unrealistic) assumptions:
* the MDP has a finite set of states and actions, which can be both tabularised
* the transition function is known

The first item means that in particular we can write programs including *for loops* on the set of states and actions,
in other words sweep over all possible states and actions.
Why making these assumptions if they are not realistic? Because the algorithms we will construct in this setting will be useful for the more general setting.

We start with a simple lemma saying that Markovian strategies are as powerful as general ones.
Recall that a Markovian strategy chooses a distribution on actions based only on the current state and the current time step,
i.e. $$\sigma' : \N \times S \to \Dist(A).$$

> **Lemma** For any (general) strategy $\sigma$, there exists a Markovian strategy $\sigma'$ such that for all time step $t$ we have 
$$
\P_{\sigma,s_0}(S_t = s, A_t = a) = \P_{\sigma',s_0}(S_t = s, A_t = a)
$$ 
implying that
$$
\E_{\sigma,s_0}[R_t] = \E_{\sigma',s_0}[R_t]
$$ 

The proof is simply to define $\sigma'$ as follows: after $t$ steps from the state $s$, play action $a$ with probability
$$\P_{\sigma,s_0}(A_t = a \mid S_t = s).$$

### The finite horizon case

We consider plays of length (exactly) $$T$$ a fixed finite value.

We let $$\val_*(t,s)$$ denote the optimal expected total reward from $s$ in $t$ moves.
The goal is to construct an optimal strategy $$\sigma$$, i.e. maximising the expected total payoff:

$$
\val_\sigma(T,s) = \E_\sigma[ \sum_{t = 1}^T R_t ] = \val_*(T,s)
$$

> **Lemma** $$\val_*(0,s) = 0$$ and 
$$
\val_*(t,s) = \max_{a \in A} \sum_{s' \in S, r \in R} \Delta(s,a)(s',r) (r + \val_*(t-1,s'))
$$

To see that this equation is correct, we remark that the value function for a (Markovian) strategy $$\sigma$$ satisfies the recursive equation:

$$
\val_\sigma(t,s) = \sum_{a \in A} \sigma(s)(a) \sum_{s' \in S,r \in \R} \Delta(s,a)(s',r) (r + \val_\sigma(t-1,s'))
$$

The key argument uses convexity.
(A very similar proof will be given below in more details in the discounted case.)

The lemma in particular implies that there are optimal deterministic Markovian strategies. 
The dependence on the time step is necessary: closer to the time step $T$, it makes sense to play moves with higher immediate rewards but dire consequences,
while for a small $t$, moves should be selected to optimise not only the reward but also the states for their potential to yield higher rewards.

The equation can easily be turned into an algorithm (with pseudo-polynomial complexity), which computes $$\val_*(t,s)$$
for each $s$ for $t$ from $0$ to $T$. 

### The discounted case

For the remainder of this post we consider the discounted payoff defined by

$$
\sum_{t \ge 1} \gamma^t R_t
$$

for a fixed discount factor $$\gamma$$ less than $$1.$$

Recall that $$\val_*(s) = \sup_{\sigma \text{ strategy}} \val_\sigma(s).$$
The goal is to construct an optimal strategy $$\sigma,$$ i.e. maximising the expected discounted payoff:

$$
\val_\sigma(s_0) = \E_{\sigma,s_0} [ \sum_{t \ge 1} \gamma^t R_t ] = \val_*(s_0)
$$

We will present two algorithms:
* **value iteration**
* **policy iteration** (sometimes called **strategy iteration** or **strategy improvement**)

In the end we will argue that these algorithms both fall into a larger family of algorithms, called generalised policy iteration.

#### Characterisation of the optimal values

Let us introduce a convenient notation, the $q$-values.
For a strategy $$\sigma$$, the $q$-value is

$$
q_\sigma(s,a) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma \val_\sigma(s'))
$$

We also denote $$q_*(s,a) = \sup_{\sigma \text{ strategy}} q_\sigma(s,a).$$

> **Lemma** The optimal values satisfy the following equations
$$
\val_*(s) = \max_{a \in A} q_*(s,a)
$$

We first show an inequality. Let $\sigma$ denote an optimal strategy from $s$. Note that a priori, it may not be optimal from other states.

$$
\begin{array}{llr}
\val_*(s) & = & \val_{\sigma}(s) \\
& = & \sum_{a \in A} \sigma(s)(a) q_\sigma(s,a) \\
& \le & \sum_{a \in A} \sigma(s)(a) q_*(s,a) \\
& \le & \max_{a \in A} q_*(s,a) & \text{ by convexity}
\end{array}
$$

To show the converse inequality, let $a$ reaching the maximum in the term on the right hand side. 
Let $\sigma_s$ denote an optimal strategy from $$s,$$ for each $$s.$$
We define $\sigma$ the strategy playing first $a$, and then simulating $$\sigma_s$$ from $$s$$ each each $$s.$$
Then 

$$
\val_{\sigma}(s) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma \val_{\sigma_{s'}}(s')) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma \val_*(s')) = q_*(s,a)
$$

By definition $$\val_{\sigma}(s) \le \val_*(s)$$, so we proved the converse inequality.

#### The value iteration algorithm

We consider functions $$v : S \to \R$$ as vectors $$v \in \R^S$$, and write $$|v|$$ for the infinity norm of $$v$$.
We extend the notation for the $q$-values to $v$, so

$$
q_v(s,a) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma v(s'))
$$

Let us denote by $$L$$ the operator $$L : \R^S \to \R^S$$ defined by

$$
L(v)(s) = \max_{a \in A} q_v(s,a).
$$

Now the lemma above reads: 
The vector $$\val_*$$ is a fixed point of $$L.$$

> **Lemma** The operator $$L$$ is $$\gamma$$-Liptschitz, meaning for any $$v,v'$$ we have
$$
|L(v) - L(v')| \le \gamma |v - v'|.
$$

It follows from Banach fixed point theorem (in the complete space $$\R^S$$ equipped with the infinity norm) 
that $$L$$ has a unique fixed point and that it can be computed as follows:
let $$v_0$$ an arbitrary vector, define $$v_{n+1} = L(v_n)$$, then

$$|v_{n+1} - v_n| \le \gamma^n |v_1 - v_0|$$

implying that $$(v_n)_{n \in \N}$$ is a Cauchy sequence, hence converges to $$v_\infty$$ such that $$v_\infty = L(v_\infty)$$,
and since $$\val_*$$ is the unique fixed point of $$L$$ we have $$v_\infty = \val_*.$$

We also get an upper bound on the convergence rate:

$$|\val_* - v_n| \le \frac{\gamma^n}{1 - \gamma} |v_1 - v_0|$$

##### The complete algorithm

We obtain an approximation algorithm: for a fixed $$\varepsilon > 0$$,
* By iterating the operator $$L$$ from any initial vector, compute $$v$$ such that $$\|L(v) - v\| \le \frac{\varepsilon}{2}$$,
implying that $$\|\val_* - v\| \le \varepsilon$$
* Construct a pure positional strategy $\sigma$: define $$\sigma(s)$$ to be an action $$a$$ such that 

$$
|v(s) - q_v(s,a)| \le \varepsilon.
$$

Then $$\sigma$$ is an $$\varepsilon$$-optimal strategy, meaning that 

$$
\E_{\sigma,s_0} [ \sum_{t \ge 1} \gamma^t R_t ] \ge \val_*(s_0) - \varepsilon
$$

Thanks to the upper bound on the convergence rate, the number of iterations of the operator $$L$$ is $$O(\log(\frac{1}{\varepsilon})).$$
Why is it called **value iteration**? Because it never considers any strategy, it only iterates on value functions.


#### The policy iteration algorithm

This second algorithm manipulates only pure positional strategies. We define two tasks:
* **evaluation**: given a strategy $$\sigma$$, compute (or approximate) $$\val_\sigma$$
* **improvement**: given a strategy $$\sigma$$ and its (approximate) value function $$\val_\sigma$$, construct an improved strategy $$\sigma'$$

##### The evaluation task

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

##### The improvement task

Given a strategy $$\sigma$$ and its value function $$\val_\sigma$$ (we ignore the fact that we actually only compute an approximation!), 
we define 

$$
\sigma'(s) = \text{argmax}_{a \in A} q_{\sigma}(s,a)
$$

> **Lemma** We have $$\val_{\sigma'} \ge \val_{\sigma}$$.
Further, if for some $$s$$ we have $$q_{\sigma}(s,a) > \val_{\sigma}(s)$$,
then the inequality above is strict for $$s.$$

To see that the lemma holds, we write the equations for $$\val_\sigma$$ and $$\val_{\sigma'}$$ and compare them.

##### The complete algorithm

The policy iteration algorithm is: 
starting from any strategy, apply alternatively evaluation and improvement to construct better and better strategies.
Note that since we consider only pure positional strategies, which are in finite number, and that each one strictly improves over the previous one,
then the algorithm terminates (that is, assuming we indeed compute the value function exactly, which we don't!).
When it does so, the last strategy $\sigma$ satisfies $$\val_\sigma(s) = \max_{a \in A} q_\sigma(s,a)$$,
which as we have seen above implies that it is optimal.

Why is it called **policy iteration**? Because it iterates on strategies (policies).

#### The generalised policy iteration algorithm

The generalised policy iteration algorithm simply alternates between evaluation and improvements steps, possibly not performing perfect evaluations.

Let us get back to the value iteration algorithm, and observe that in some sense it also consists in alternating evaluation and improvement steps.
At every step, we have a value function $$v_n$$ and the algorithm computes $$v_{n+1} = L(v_n)$$ defined by

$$
v_{n+1}(s) = \max_{a \in A} q_{v_n}(s,a).
$$

We can interpret this as doing the following two steps in one go:
* from $$v_n$$ compute the $q$-value $$q_{v_n}(s,a)$$ through one step of the evaluation task
* construct the value function $$v_{n+1}$$ induced by $$q_{v_n}$$ through the improvement task

The main difference with policy iteration is that $$v_n$$ does not represent the values of a given strategy.

