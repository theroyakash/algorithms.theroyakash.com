# Maths problems in CSES

## Counting Divisors
Given $n$ integers, your task is to report for each integer the number of its divisors. For example, if $x=18$, the correct answer is $6$ because its divisors are $1,2,3,6,9,18$.

### Approach
- We start with the prime factorization of the number $n$.
- If the prime factorization is $n = \prod_{i=1}^{k} P_i^{\alpha_i}$, then number of factors are $\prod_{i=1}^{k} (\alpha_i + 1)$, here $(\alpha_i + 1)$ many ways it can appear in the number $n$.

Any divisor $d$ of $n = \prod_{i=1}^{k} p_i^{\alpha_i}$ must take the form $d = \prod_{i=1}^{k} p_i^{\beta_i}$, where each exponent satisfies $0 \leq \beta_i \leq \alpha_i$, giving exactly $(\alpha_i + 1)$ independent choices per prime. Since the choices are independent across all primes, the multiplication principle gives the total number of divisors as $\tau(n) = \prod_{i=1}^{k} (\alpha_i + 1)$.

### Code
```cpp
void solve() {
    map<int, int> cntr;
    int n, factors = 1; cin >> n;
    for (int x = 2; x*x <= n; x++) {
        while (!(n%x)) {
            cntr[x]++;
            n/=x;
        }
    }

    if (n > 1) cntr[n]++;

    for (auto &[prime, count] : cntr) {
        factors *= (count + 1);
    }

    std::cout << factors << "\n";
}
```