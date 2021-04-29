# (G) Bayesian approach: Thompson's sampling algorithm 

The general idea is that we have an initial prior distribution of the data or belief, it is also called bias.
Then we observe new data, so we need to update our belief to take this new data into account.
We use Bayes' rule to update our prior belief into a posterior belief.
Then this procedure is repeated each time we observe new data, the posterior at time $t-1$ becomes the prior at time step $t$.

Thompson sampling builds a *prior* distribution of the reward of each machine.
Then at each step, it samples one reward for each machine and plays the machine with the best sample.
After observing the reward, the *prior* distribution is updated into a *posterior* distribution using Bayes' rule.
This results in the following algorithm with ```priors[i-1]``` being the reward distribution model of machine $i$:


```python
def thompson_sampling(K: int, T: int, priors: List):
    # Initialize list
    theta = [0 for _ in range(K)]
    # Set distributions to priors
    distributions = priors

    for t in range(T):
        # Take a sample for each machine
        for i in range(K):
            theta[i] = sample_from(distributions[i])

        # Choose machine with best sampled reward
        a_t = argmax(theta)
        # Play machine a_t and observe r_t := X(a_t, t)
        r_t = play(a_t)
        
        # Replace distributions with posteriors
        for i in range(K):
            distributions[i] = bayesian_update(distributions[i], a_t, r_t)
```


It should be clear that a key element is that we should be able to keep track analytically of the posterior.
In practice, this reduces the choice of priors and likelihood distributions and thus of the modeling however approximations are possible.

One advantage of Thompson sampling is that it provides a distribution of the reward of machine $i$ at the current timestep.
It enables to eventually compute other quantities of interest that might not be computable otherwise.