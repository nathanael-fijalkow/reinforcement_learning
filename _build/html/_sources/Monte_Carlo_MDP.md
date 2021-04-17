---
layout:     post
title:      Part 3, Monte Carlo approaches, temporal differences, and off-policy learning
date:       2019-11-28 9:00:00
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

<p class="intro"><span class="dropcap">W</span>e consider the setting where the MDP is only known through simulation and show how to adapt the previous algorithms using statistics instead of exact computations.</p>

The basic notations are given in the [course outline]({{ '/blog/MDP' | prepend: site.baseurl }}).
The first part about dynamic algorithms is [here]({{ '/blog/dynamic_algorithms_MDP' | prepend: site.baseurl }}).

We now lift the two (unrealistic) assumptions made by dynamic algorithms:
* the MDP has a finite set of states and actions, which can be both tabularised
* the transition function is known

The algorithmic setting is as follows. 
There is an underlying MDP dictating the dynamics, but the controller does not know it (initially), he acquires knowledge of the MDP through interaction.

At each time step the controller knows in which state is the MDP and the set of actions available at this state. 
(In our original definition, the set of actions is the same for every states, but sometimes it is useful to refine this and have different sets of actions for each state.)
This means that states can be finitely represented, but we do not assume that there are finitely many, and more importantly we do not assume that we know what is the set of all states.
From a state the controller triggers an action, which results in a reward and moving to the next state.
The controller **does not know the transition distribution**: it is sampled each time the controller triggers an action, which means that the controller can make statistics, but he never knows exact probabilities.

## The Monte Carlo approach

The dynamic algorithms we constructed in the first part are based on solving two tasks:
* **evaluation**: given a strategy $$\sigma$$, compute (or approximate) $$\val_\sigma$$
* **improvement**: given a strategy $$\sigma$$ and its (approximate) value function $$\val_\sigma$$, construct an improved strategy $$\sigma'$$

We will keep the general architecture of the algorithms, while adapting the solution of the two tasks to our statistical setting.

In the dynamic algorithms we were only manipulating pure positional strategies. 
Here we will see that it will be useful to allow randomised strategies.

The Monte Carlo approach makes one key assumption:
* **finite episodes**: every play terminates whatever the choice of actions, which means reaches a state called terminal, where there are no further actions. 
This is reasonable for most games, where a winner is eventually determined, in finite time.
This assumption also means that whenever a terminal state is reached we can start over from the initial state again to produce a new episode.

##### The evaluation task

Recall that the dynamic solution for the evaluation task was based on the following equations for a strategy $$\sigma:$$

$$
\val_\sigma(s) = q_\sigma(s,\sigma(s)) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma \val_\sigma(s')).
$$

The problem is that now we do not assume that we know $$\Delta(s,a),$$ so this quantity is not easily computed.

The most naive and yet effective solution is simply to sample plays according to $$\sigma$$.
Starting from the initial state $$s_0,$$ choose the action prescribed by $$\sigma$$ and continue until reaching a terminal state.
Repeat this operation a large number of times, say $$N$$, leading to a sample of $$N$$ plays.
For each state $$s$$, for each play containing $$s$$ compute the total reward **from the first visit** of $$s$$ in that play,
and set $$\widehat{\val}_\sigma(s)$$ to be the average of these total rewards.
The hat over $$\val$$ signifies that the value is **empirical**.
Using the equation above $$\widehat{\val}_\sigma(s)$$ induces $$\widehat{q}_\sigma.$$

Note that the only design choice so far in the naive Monte Carlo approach was whether we consider each visit or only the first in a play.
Both would lead to converging algorithms, as long as we evaluate randomised positional strategies.

##### The improvement task

Recall that given a strategy $$\sigma$$ and its value function $$\val_\sigma$$, 
we define 

$$
\sigma'(s) = \text{argmax}_{a \in A} q_{\sigma}(s,a)
$$

The problem here is that since we will be evaluating $$\sigma$$ using statistics on the plays of $$\sigma$$, 
we do not have any information about the outcomes of the actions that are not played by $$\sigma$$. 
In other words, if $$\sigma$$ does not maintain **exploration**, then our value function $$\val_\sigma$$ will not give us information outside of the behaviour of $$\sigma$$, 
hence not allow us to try interesting new moves.

There are different ways to ensure **exploration**, a prominent one being to use $$\varepsilon$$-greedy strategies,
leading to the following solution for the improvement task:

$$
\sigma'(s) = 
\begin{cases}
\text{argmax}_{a \in A} q_{\sigma}(s,a) & \text{ with probability } 1 - \varepsilon \\
\text{uniform distribution on actions} & \text{ with probability } \varepsilon
\end{cases}
$$

The fact that the strategy plays each action with positive probability implies that when evaluating $$\sigma'$$ we collect information about each action for every visited state.
Note that some states may never be visited, that's fine.

##### The complete algorithm

The Monte Carlo policy iteration algorithm is: 
starting from any strategy, apply alternatively evaluation and improvement to construct better and better strategies.
The theoretical guarantees are slim, since unless we evaluate strategies for infinitely many times, $$\widehat{\val}_\sigma$$ may not be equal (or even close!) to $$\val_\sigma.$$
But in practice, it works.

## The temporal difference approach

Coming soon!

## Learning off-police and importance sampling

Coming soon (but less soon than above...)!


