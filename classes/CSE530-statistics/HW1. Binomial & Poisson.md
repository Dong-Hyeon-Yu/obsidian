
> Binomial distribution has very similar shape with Poisson distributions when $n$ is large and $p$ is small. 
### 1. Figure
![[binomial_approximation(lambda=60).png]]
- **`Poisson distribution`**: blue filled histogram ($\lambda$=60)
- **`Binomial distributions`**: lined graphs (adjust $n$ and $p$, s.t. $n \times p = \lambda$ )

### 2. Python codes
```python
def draw_binomial_and_poison(mu: int):
    p = np.arange(1, 10, 2) / 100
    p = np.append(p, np.arange(1, 10) / 10)
    n = mu / p

    plt.figure(figsize=(mu*0.5, 8))

    """ Poisson distribution """
    x = np.arange(mu*0.5, mu * 1.5)
    plt.hist(x=x, weights=stats.poisson.pmf(k=x, mu=mu), label=f"poisson(λ={mu})", alpha=0.7, density=True,
             histtype='stepfilled', bins=mu)

    """ Binomial distributions """
    pal_brbg = sns.color_palette("BrBG", len(n))
    for i, (_n, _p) in enumerate(zip(n, p)):
        y = stats.binom(n=_n, p=_p).pmf(x)

        plt.plot(x, y, color=pal_brbg[i], label=f"n={_n:.1f}, p={_p:.2}")
        plt.scatter(x, y, color=pal_brbg[i])

    plt.ylabel("Probability")
    plt.title(f"Binomial Approximation (λ = [{mu}])")
    plt.xticks(x)
    plt.grid(axis="y", linestyle="--", color="#CCCCCC")
    plt.legend(loc="upper right")
    plt.show()

if __name__ == "__main__":
    for i in range(10, 101, 10):
        draw_binomial_and_poison(i)
```
