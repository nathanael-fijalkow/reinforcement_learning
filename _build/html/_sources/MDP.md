---
layout:     post
title:      Course outline
date:       2019-09-26 9:00:00
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

<p class="intro"><span class="dropcap">W</span>e present the outline of a course on reinforcement learning.</p>

The reference book and key inspiration for this course and for reinforcement learning in general is the book by [Sutton and Barto](http://incompleteideas.net/book/the-book.html) (it can be downloaded for free at that address).
The reason for these lecture notes to exist is that we will have a more theoretical focus and we will provide toy code to play with the different algorithms.

#### Part 1: Multi-armed Bandits

We introduce the model of multi-armed bandit and discuss the exploration / exploitation trade-off.
As a technical result we give a full proof for the [analysis of the UCB algorithm for multi-armed bandits]({{ '/blog/armed_bandits_upper_bound' | prepend: site.baseurl }}).

There is an accompanying github repository for experimenting with different algorithms for the [multi-armed bandits](https://github.com/nathanael-fijalkow/Multi-Armed-Bandits).

#### Part 2: Markov decision processes and dynamic algorithms

We discuss the model of [Markov decision processes]({{ '/blog/dynamic_algorithms_MDP' | prepend: site.baseurl }}) and construct algorithms in the often unrealistic scenario where the whole MDP is both finite and fully known, called dynamic algorithms.

#### Part 3: Monte Carlo approaches, temporal differences and off-policy learning

We explain how to lift the assumption that the whole MDP is known by considering [statistical approaches]({{ '/blog/Monte_Carlo_MDP' | prepend: site.baseurl }}): Monte Carlo and temporal difference.
We also discuss the role of on- and off-policy learning.

#### Part 4: Two-player games and self-play

We consider how to extend the algorithms for MDPs to two-player games, introducing the key idea of self-play.
One of the most successful algorithm is the [Monte Carlo tree search algorithm]({{ '/blog/monte_carlo_tree_search' | prepend: site.baseurl }}).

There is an accompanying github repository for implementations of some [algorithms solving Tic-Tac-Toe](https://github.com/nathanael-fijalkow/TicTacToe_RL).

#### Part 5: Deep reinforcement learning

We lift the assumption that the MDP is known and introduce function approximation using various models, such as linear models or neural networks.

