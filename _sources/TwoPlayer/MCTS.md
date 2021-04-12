# Monte Carlo Tree Search (MCTS) for two-player games

We present the Monte Carlo tree search algorithm for finding an optimal strategy in a two-player game.

There is an accompanying github repository for implementations of some [algorithms solving Tic-Tac-Toe](https://github.com/nathanael-fijalkow/TicTacToe_RL).

There is a zoo of "Monte Carlo search tree algorithms", and finding better and better instances is a very active research question nowadays.
Among them, the UCT (Upper Confidence bounds for Trees) is special because it was among the first one and it had a decisive impact on IA for Go playing.
It was introduced by Kocsis and Szepesvári in [this paper](https://sites.ualberta.ca/~szepesva/papers/ecml06.pdf).

The basic idea is simple: **bias the sample procedure (choosing which children to explore) by the current estimates of their values**.

Each node $v$ induces an instance of the multi-armed bandit problem: 
each child is seen as a machine with some unknown distribution that can be evaluated.
The choice of the next machine to be evaluated is in the hands of a UCB algorithm, 
which maintains for each machine $v'$ an interval $I(v',t)$ (evolving with the time step $t$), and chooses the machine with the maximal (or minimal) value with respect to $I$.
More specifically, the definition of $I(v',t)$ for a child $v'$ of a node $v$ is

$$
I(v',t) = [ \widehat{X}(v',t) - \sqrt{ \frac{\log(n(v,t))}{n(v',t)} }, \widehat{X}(v',t) + \sqrt{ \frac{\log(n(v,t))}{n(v',t)} } ]
$$

The term $\widehat{X}(v',t)$ is the empirical average outcome of the observed plays including $v'$, 
and the term $\sqrt{ \frac{\log(n(v,t))}{n(v',t)} }$ quantifies the accuracy of the first term (in particular, it decreases with $n(v',t)$, the number of times $v'$ has been seen until time $t$).

The algorithm goes as follows.
We sample plays starting from the root. During the sampling from a node $v$, there are two cases:
* *selection*: either all children of the node have already been explored (implying that there are values $I(v',t)$ for all children of $v$), 
then the child is chosen by optimising the optimising value of $I$ among children (the treatment is different for the two players: Max maximises the upper bound, while Min minimises the lower bound)
* *expansion and simulation*: or some child of the node has never been explored, and then a child is chosen at random among those

Once a play has reached a terminal node, we *update* the values of all the nodes along the play.

This algorithm is one of the main ingredients in the AlphaGo and AlphaGo Zero projects to beat human players at Go.
Interestingly, its theoretical properties are not very good, for a simple reason: 
the values of $I$ do not (provably) accurately reflect the actual values.
The original paper proves that the regret grows logarithmically with time (altough I could not find the original proof of this: only the short version of the paper seems to exist!).
However, a very nice and simple example due to Coquelin and Munos (see the paper [here](https://arxiv.org/abs/1408.2028)) shows that the regret can be very large
because the algorithm may fail to detect the optimal path if they are to be found among worse and worse paths (and some claim this explains how Lee Seedol won one match against AlphaGo!).


