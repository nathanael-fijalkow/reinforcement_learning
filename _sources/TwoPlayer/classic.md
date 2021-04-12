# Classical algorithms

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
