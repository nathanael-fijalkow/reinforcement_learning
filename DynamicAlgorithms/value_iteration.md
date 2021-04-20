# Value iteration algorithm

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

## The complete algorithm

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
