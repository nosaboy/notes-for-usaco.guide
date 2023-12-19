## CPH Chapter 22 Combo
We can use recursion to solve combination problems. However, some recursion can be **generalized into close formula** just like in contest math combinatorics.

### Binomial Coefficients
We can calculate $\binom{n}{k}$ using the following two formulas:
- $\binom{n}{k} = \binom{n-1}{k-1} + \binom{n-1}{k}$, base case - $\binom{n}{0} = \binom{n}{1} = 1$, using recursion
- $\binom{n}{k} = \frac{n!}{k!(n-k)!}$ by precomputing factorials

### Basic Properties:
- $\binom{n}{k} = \binom{n}{n-k}$
- $\binom{n}{0} + \binom{n}{1} + \binom{n}{2} + \cdots + \binom{n}{n} = 2^n$
- Pascals Triangle

### Balls and Boxes: ways to place k balls in n boxes

- If each box can contain at most 1 ball: We choose k boxes from n boxes - $\binom{n}{k}$
- If each box can contain multiple balls: We place n-1 dividers for n boxes and k stars for k balls(Stars and Bars) - $\binom{n-1+k}{k}$
- If each box can contain multiple balls but no two adjacent boxes both contain a ball:$\binom{n-k+1}{n-2k+1}$

**Multivariable Coefficient(generalizatiobn of binomial coefficient):** $\binom{n}{k_1,k_2,...,k_m} = \frac{n!}{k_1!k_2!...k_m!}$ where $k_1 + k_2 + ... + k_m = n$

is the number of ways to split n objects into boxes/subsets of sizes $k_1, k_2, ..., k_m$ at once.

### Catalan Numbers
$C_n$ = number of valid parenthesis expressions with n left brackets and n right brackets
Valid parenthesis expression: an expression with exactly n left and n right brackets such that if we chose any prefix of the expression, the number of right brackets <= number of left brackets -> Follows:
- empty expression is valid
- if expression A is valid, (A) is valid
- if expression A and B are both valid, AB is valid

#### Recursive Formula: $C_n = \sum_{i=0}^{n} C_iC_{n-i-1}$

#### Closed Formula: $C_n = \frac{1}{n+1} \binom{2n}{n}$
$\binom{2n}{n}$ is the number of ways to choose a parenthesis expression(might not be valid: just placing n left parenthesis in 2n avaliable spots).

#### Counting Trees
The number of binary trees with n nodes is $C_n$
The number of normal trees(rooted at some node) with n nodes is $C_{n-1}$


### PIE



#### Derangements: a permutation of 1...n such that no numbers are in their original sorted spot
We can use PIE and count the number of ways at least 1 number is in its sorted spot, then complementary counting.

This is just we fix one of the values, then two of the values, and so on: n choose 1 * (n-1)! - n choose 2 * (n-2)! + ... + n choose n * 1

**Recursion to calculate derangements of permutation n(f(n)):** 
if n = 0: return 1;
if n = 1: return 2;
else if n > 1: return (n-1)(f(n-1) + f(n-2))

### Burnsides Lemma




**Example 1:**

```cpp

/** Computes x^n modulo m in O(log p) time. */
ll qexp(ll a, ll b, ll m) {
    ll res = 1;
    while (b) {
        if (b % 2) res = res * a % m;
        a = a * a % m;
        b /= 2;
    }
    return res;
}
vector<ll> fact, invf;
 
void precompute(int n) {
    /** Precomputes n! from 0 to MAXN. */
    fact.assign(n + 1, 1); 
    for (int i = 1; i <= n; i++) fact[i] = fact[i - 1] * i % MOD;
    invf.assign(n + 1, 1);
    /**
    * Precomputes all modular inverse factorials
    * from 0 to MAXN in O(n + log p) time
    */
    invf[n] = qexp(fact[n], MOD - 2, MOD);
    for (int i = n - 1; i > 0; i--) invf[i] = invf[i + 1] * (i + 1) % MOD;
}
/** Computes nCr mod p */
ll nCk(ll n,ll k) {
    if (k < 0 || k > n) return 0;
    return fact[n] * invf[k] % MOD * invf[n - k] % MOD;
    // return fact[n] * qexp(fact[k], MOD - 2, MOD) % MOD * qexp(fact[n - k], MOD - 2, MOD) % MOD;
}
 
void solve(){
    int q;cin>>q;
    while(q--){
        int a,b;cin>>a>>b;
        cout<<nCk(a,b)<<"\n";
    }
 
}


// make sure to do precompute(1000005 = 10^6) in main
```


**Problem 4:** https://codeforces.com/contest/1462/problem/E2

Time: 20 minutes

Self Editorial:
We note that after sorting the array, the min and max will just be the left and right bounds such that their difference is no more than k. We then note that we can choose any m people in this range. Let us fix the left bound by looping through it. We want the total distinct ways, so lets sum up all m tuples where v_i is the smallest in the tuple(curr left bound). We then use two pointers and increase until we reached a right bound(the max element such that v_r - v_i <= k). We first pick v_i into the tuple. We now have r - i choices and m-1 spots left. Thus we add (r-i choose m-1).
We precompute nCk using a template function.

```cpp
ll qexp(ll a, ll b, ll m) {
    ll res = 1;
    while (b) {
        if (b % 2) res = res * a % m;
        a = a * a % m;
        b /= 2;
    }
    return res;
}
vector<ll> fact, invf;

// precompute factorial and n choose k
void precompute(int n) {
    fact.assign(n + 1, 1); 
    for (int i = 1; i <= n; i++) fact[i] = fact[i - 1] * i % MOD;
    invf.assign(n + 1, 1);
    invf[n] = qexp(fact[n], MOD - 2, MOD);
    for (int i = n - 1; i > 0; i--) invf[i] = invf[i + 1] * (i + 1) % MOD;
}
 
ll nCk(ll n,ll k) {
    if (k < 0 || k > n) return 0;
    return fact[n] * invf[k] % MOD * invf[n - k] % MOD;
    // return fact[n] * qexp(fact[k], MOD - 2, MOD) % MOD * qexp(fact[n - k], MOD - 2, MOD) % MOD;
}
 

void solve(){
    int n,m,k;cin>>n>>m>>k;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end());
    int it = 0;
    int ans = 0;
    rep(i,0,n){
        // finds largest bound such that the max - v_i <= k
        while(it+1 < n && v[it+1] - v[i] <= k){
            it++;
        }
        ans = add(ans, nCk(it - i, m-1)); // # of choosings with ith element included
      // number of elements from i+1...it choose m-1, since we chose v[i] as our mth element, m-1 spots left.
    }
    cout<<ans<<endl;
}

// NOTE: IN THE MAIN FUNCTION, PRECOMPUTE FACTORIAL. WRITE: precompute(200005);
```
