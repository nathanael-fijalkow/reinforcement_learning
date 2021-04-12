# The model of two-player games

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
