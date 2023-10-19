
> Binomial distribution has similar form with Gaussian distribution when the number of trials $n$ is large.
 
### 1. Figures
- **`Gaussian Distribution`** : blue filled histogram ($n$=80)
- **`Binomial Distribution`** : lined graphs (adjust $p$, s.t. $t \in \{0.15, 0.5, 0.85\}$ )
#### (1) $n$=80, $p$=0.15 (left-skewed)
![[Demoivre-Laplace_theorem(n=80,p=0.15).png]]

#### (2) $n$=80, $p$=0.5 (symmetric)
![[Demoivre-Laplace_theorem(n=80,p=0.5).png]]

#### (3) $n$=80, $p$=0.85 (right-skewed)
![[Demoivre-Laplace_theorem(n=80,p=0.85) 1.png]]

### 2. Python codes
```python
def draw_gaussian_binomial(n: int, p: float):
    mu = n*p
    stdev = math.sqrt(mu*(1-p))

    plt.figure(figsize=(n*0.3, 8))

    """ gaussian distribution """
    _x = np.linspace(stats.norm.ppf(0.001, loc=mu, scale=stdev),
                    stats.norm.ppf(0.999,  loc=mu, scale=stdev), 300)
    plt.hist(x=_x, weights=stats.norm.pdf(_x, loc=mu, scale=stdev), bins=300, density=True, alpha=0.5,
             histtype='stepfilled', label="Gaussian")

    """ binomial distribution as n increases """
    n_var = np.arange(10, n+1, 10)
    pal_brbg = sns.color_palette("BrBG", len(n_var))
    x = np.arange(min(0, mu-3*stdev), mu + 3 * stdev)
    for i, _n in enumerate(n_var):
        y = stats.binom(n=_n, p=p).pmf(x)
        plt.plot(x, y, color=pal_brbg[i], label=f"n={_n}")
        plt.scatter(x, y, color=pal_brbg[i])

    plt.ylabel("Probability")
    plt.title(f"Demoivre-Laplace theorem (n = [{n}], p = [{p:.1f}])")
    plt.xticks(x)
    plt.grid(axis="y", linestyle="--", color="#CCCCCC")
    plt.legend(loc="upper right")
    plt.show()


if __name__ == '__main__':
    draw_gaussian_binomial(80, 0.15)
    draw_gaussian_binomial(80, 0.5)
    draw_gaussian_binomial(80, 0.85)
```