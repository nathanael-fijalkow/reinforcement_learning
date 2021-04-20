# (G) Values

Let us introduce a convenient notation, the $q$-values.
For a strategy $$\sigma$$, the $q$-value is

$$
q_\sigma(s,a) = \sum_{s',r} \Delta(s,a)(s',r) (r + \gamma \val_\sigma(s'))
$$

We also denote $$q_*(s,a) = \sup_{\sigma \text{ strategy}} q_\sigma(s,a).$$
