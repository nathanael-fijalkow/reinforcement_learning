# Dynamic algorithms

Dynamic algorithms make two (unrealistic) assumptions:
* the MDP has a finite set of states and actions, which can be both tabularised
* the transition function is known

The first item means that in particular we can write programs including *for loops* on the set of states and actions,
in other words sweep over all possible states and actions.
Why making these assumptions if they are not realistic? Because the algorithms we will construct in this setting will be useful for the more general setting.

We start with a simple lemma saying that Markovian strategies are as powerful as general ones.
Recall that a Markovian strategy chooses a distribution on actions based only on the current state and the current time step,
i.e. 

$$
\sigma' : \N \times S \to \Dist(A).
$$

> **Lemma** For any (general) strategy $\sigma$, there exists a Markovian strategy $\sigma'$ such that for all time step $t$ we have 
$\P_{\sigma,s_0}(S_t = s, A_t = a) = \P_{\sigma',s_0}(S_t = s, A_t = a)$ 
implying that
$\E_{\sigma,s_0}[R_t] = \E_{\sigma',s_0}[R_t]$.

The proof is simply to define $\sigma'$ as follows: after $t$ steps from the state $s$, play action $a$ with probability

$$
\P_{\sigma,s_0}(A_t = a \mid S_t = s).
$$
