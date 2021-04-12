# Bellman equations: characterisation of the optimal values

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
