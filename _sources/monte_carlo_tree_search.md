---
layout:     post
title:      Monte Carlo Tree Search 
date:       2019-11-25 9:00:00
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
      A: "{\\mathcal{A}}",
      B: "{\\mathcal{B}}",
      E: "{\\mathbb{E}}",
      P: "{\\mathbb{P}}",
      val: "{\\text{val}}",
    }
  }
});
</script>

<p class="intro"><span class="dropcap">W</span>e present the Monte Carlo tree search algorithm for finding an optimal strategy in a two-player game.</p>

There is an accompanying github repository for implementations of some [algorithms solving Tic-Tac-Toe](https://github.com/nathanael-fijalkow/TicTacToe_RL).

The setting we consider is two-player (called Max and Min) games played on a tree, with perfect imperfect and turn-based moves.
Adding random moves or simulatenous moves (also called concurrent) would not make the question much more complicated (but at the price of heavier notations).
Considering multi-agents or imperfect information however is significantly harder and we will not touch upon these topics here.

One assumption we make here is that the outcome of a play **can only be determined at the end of the play**.
For win/lose games the outcome is 1 (win) or 0 (lose), but we can also consider more complicated (numerical) outcomes (in [0,1] for the sake of simplicity).
The fact that the quality of a play cannot be (a priori) evaluated along the way is a key difficulty we are facing here.

The goal is to find an optimal strategy.
Towards this goal we compute the function $$\val : N \to [0,1]$$ where $$N$$ is the set of nodes of the tree,
defined by

$$
\val(v) = \sup_{\sigma \text{ strategy of Max}} \inf_{\tau \text{ strategy of Min}} \text{Outcome}_v(\sigma,\tau)
$$

In words: $\val(v)$ is the best outcome Max can secure against any strategy of Min starting from the node $v$.

### Some classical algorithms

The very first algorithm is **min max**, it simply consists in unravelling the definition of $$\val$$:

$$
\val(v) = 
\begin{cases}
\max_{v' \text{child of } v} \val(v') & \text{if } v \text{ is controlled by Max} \\ 
\min_{v' \text{child of } v} \val(v') & \text{if } v \text{ is controlled by Min}
\end{cases} 
$$

Unfortunately, this algorithm is in practice way too computationally expensive, for two reasons: for most games, 
* the *depth* of the tree (length of a play) is too large,
* and most importantly, the *branching factor* (number of possible moves from a position) is too large.

The first improvement is just to do lazy evaluations, sometimes called **alpha beta pruning**. 
Concretely, if the outcomes are $0$ and $1$: whenever some child of a Max node has value $1$, the value of the node becomes $1$, without further computation.
For outcomes in $[0,1]$, each node is assigned two values, $\alpha$ and $\beta$, which are lower and upper bounds on the value of the node.
The current evaluation can be used to discard suboptimal moves.

Although this does lead to performance improvement in some cases, it is not enough to tackle most games.
An approach we do not discuss here is **iterative deepening**: we fix a depth and stop the search after this depth, evaluating the plays thus far.
This requires having an evaluation of partial plays, which may not be easy to find (for instance for Go).

The natural next idea is to add randomisation using a **Monte Carlo** approach: rather than enumerating for each node all of the children,
we randomly sample a subset of them. 
The naive Monte Carlo approach performs (often) better than the deterministic algorithms, but the real game changer is yet to come.

### Monte Carlo search tree: the UCT algorithm

There is a zoo of "Monte Carlo search tree algorithms", and finding better and better instances is a very active research question nowadays.
Among them, the UCT (Upper Confidence bounds for Trees) is special because it was among the first one and it had a decisive impact on IA for Go playing.
It was introduced by Kocsis and Szepesvári in [this paper](https://sites.ualberta.ca/~szepesva/papers/ecml06.pdf).

The basic idea is simple: **bias the sample procedure (choosing which children to explore) by the current estimates of their values**.

Each node $v$ induces an instance of the multi-armed bandit problem 
(see this [blog post]({{ '/blog/armed_bandits_upper_bound' | prepend: site.baseurl }}) for some background on the problem and an analysis of the UCB algorithm): 
each child is seen as a machine with some unknown distribution that can be evaluated.
The choice of the next machine to be evaluated is in the hands of a UCB algorithm, 
which maintains for each machine $$v'$$ an interval $$I(v',t)$$ (evolving with the time step $t$), and chooses the machine with the maximal (or minimal) value with respect to $$I$$.
More specifically, the definition of $$I(v',t)$$ for a child $v'$ of a node $v$ is

$$
I(v',t) = [ \widehat{X}(v',t) - \sqrt{ \frac{\log(n(v,t))}{n(v',t)} }, \widehat{X}(v',t) + \sqrt{ \frac{\log(n(v,t))}{n(v',t)} } ]
$$

The term $$\widehat{X}(v',t)$$ is the empirical average outcome of the observed plays including $v'$, 
and the term $$\sqrt{ \frac{\log(n(v,t))}{n(v',t)} }$$ quantifies the accuracy of the first term (in particular, it decreases with $n(v',t)$, the number of times $v'$ has been seen until time $t$).

The algorithm goes as follows.
We sample plays starting from the root. During the sampling from a node $v$, there are two cases:
* *selection*: either all children of the node have already been explored (implying that there are values $$I(v',t)$$ for all children of $v$), 
then the child is chosen by optimising the optimising value of $I$ among children (the treatment is different for the two players: Max maximises the upper bound, while Min minimises the lower bound)
* *expansion and simulation*: or some child of the node has never been explored, and then a child is chosen at random among those

Once a play has reached a terminal node, we *update* the values of all the nodes along the play.

This algorithm is one of the main ingredients in the AlphaGo and AlphaGo Zero projects to beat human players at Go.
Interestingly, its theoretical properties are not very good, for a simple reason: 
the values of $$I$$ do not (provably) accurately reflect the actual values.
The original paper proves that the regret grows logarithmically with time (altough I could not find the original proof of this: only the short version of the paper seems to exist!).
However, a very nice and simple example due to Coquelin and Munos (see the paper [here](https://arxiv.org/abs/1408.2028)) shows that the regret can be very large
because the algorithm may fail to detect the optimal path if they are to be found among worse and worse paths (and some claim this explains how Lee Seedol won one match against AlphaGo!).


