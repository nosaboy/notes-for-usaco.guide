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



**Example 1:** https://cses.fi/problemset/task/1079

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
### PIE



### Derangements: a permutation of 1...n such that no numbers are in their original sorted spot
**Example 2:** https://judge.yosupo.jp/problem/montmort_number_mod
Count the number of derangements from 1 to n.

#### Calculate derangements of permutation n using DP:
Proof: Let $i$ be at position 1 in the final arrangement, where $i$ is any number from 1 to n. Then there are two case:
- If 1 is in position $i$, it means that 1 and i switched positions - This turns into subproblem with size n-2. 
- If 1 is not in position $i$, consider the following:
    - We have i in the 1st position. Now, we **don't want 1 in the ith position** since thats what we are counting.
    - Thus, let the ith position be a substitue for the 1st position(since its the same as not wanting 1 in the 1st position, but now we look at i as the 1st position AKA the position that we dont want 1 to be in).
    -  This then turns into subproblem with size n-1 since we do not want 1 to be in the ith position.

There are $n-1$ ways to choose $i$. For each $i$, there are $dp[n-1] + dp[n-2]$ ways to construct a derrangement. Thus, the answer is $dp[n] = (n-1)*(dp[n-1]+dp[n-2]);$
The base case is $dp[1] = 0, dp[2] = 1;$
Thus, we have the following **recursive formula**:

if n = 1: return 0;
if n = 2: return 1;
else if n > 1: return (n-1)(f(n-1) + f(n-2))

```cpp
void solve(){
    int n; cin>>n>>MOD;
    int dp[n+1]={0};
    dp[1]=0; dp[2]=1; // base case
    rep(i,3,n+1){
        dp[i] = mult(i-1, add(dp[i-1],dp[i-2])); // recursion
    }
    rep(i,1,n+1){
        cout<<dp[i]<<" ";
    }
    cout<<endl;
}   

```


#### Calculate derangements using PIE
We can use PIE and count the number of ways at least 1 number is in its sorted spot, then complementary counting.
So we want $$n! - \lvert E_1 \cup E_2 \cup \dots \cup E_n \rvert =\sum_{k=1}^n(−1)^k\cdot(number-of-permutations-with-k-fixed-points).$$
This is just we fix one of the values, then two of the values, and so on: n choose 1 * (n-1)! - n choose 2 * (n-2)! + ... + n choose n * 1.

For each $k$ we have $\binom{n}{k}(n-k)! = \frac{n!(n-k)!}{k!(n-k)!} = \frac{n!}{k!}.$
Thus, the final answer is

$$n!\sum_{k=0}^n\frac{(-1)^k}{k!}$$

NOTE ON IMPLEMENTATION: We cannot calculate division(1/k!) since **the problem inputs the mod** which may cause k! to not have an inverse since k! and m **may not be coprime**. Thus, we have to use a slick trick instead of doing $divide(1,fact[k])$ every time.
Lets transition from prev answer to curr answer.
We have $$prev = (n-1)! * sum_{k=0}^{n-1} (-1)^k/k!$$ and we want curr = $$(n)! * sum_{k=0}^{n} (-1)^k/k!$$.
First multiply prev by n, so $$sum = (n)! * sum_{k=0}^{n-1} (-1)^k/k!$$
Then add $$n! * (-1)^n/n! = (-1)^n$$ since we want $$sum_{k=0}^{n} (-1)^k/k!$$, so $$(n)! * sum_{k=0}^{n-1} (-1)^k/k! + n! * (-1)^n/n! = n! * (sum_{k=0}^{n-1} (-1)^k/k! + (-1)^n/n!) = n! * (sum_{k=0}^{n} (-1)^k/k!).$$
Thus, the transition is simply just $$curr = prev * n + (-1)^n$$ which spares us from any division by MOD.

```cpp
void solve(){
    int n; cin>>n>>MOD;
    int sum = 1;
    precompute(n+1);
    
    rep(i,1,n+1){
        int one = (i%2==0) ? 1:-1; // (-1)^i
        sum = add(mult(sum,i), one); // transition from prev 
        cout<<sum<<" ";
    }
    cout<<endl;
}   
```

**Problem 3:** https://codeforces.com/problemset/problem/888/D

Self Editorial:
For every k, we can choose 1,2,...,k positions, then set all positions as bad. That is just the derangement of length i. Then, the answer is just we add them all up. Since k <= 4, we can just calculate nCk the dumb way.
```cpp
ll nCk(ll n,ll k) {
    ll ans = 1;
    int cnt = 0;
    rep(i,0,k){
        ans *= n-cnt;
        cnt++; 
    }
    rep(i,1,k+1){
        ans /= i;
    }
    return ans;
}
void solve(){
    int n,k;cin>>n>>k;
    precompute(n);
    ll osa[5]={0}; // prefix of derangement length k
    osa[0] = 1; osa[1]=0; osa[2] = 1; osa[3] = 2; osa[4] = 9;
    rep(i,1,5){
        osa[i] *= nCk(n,i);
        osa[i] += osa[i-1];
    }
    cout<<osa[k]; // add k = 0 case: 1
}
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


**Problem 7:** https://codeforces.com/gym/104520/problem/K

Look at what each condition means:
MEX = x means that if the good subarray value is x, then the subarray must contain 1,2,...,x-1,_not x_, ...
Median = x: Since x is the median and the previous number x-1 must be in the subarray, the next number is x+1. Moreover, there must also be x-1 numbers bigger than x. So x-2 numbers bigger than x+1 and they can be any number from x+2 to n. Therefore, the length of the good subarray is 2 * (x-1).

We can thus construct a permutation based on a good subarray. First we count the permutation of the good subarray itself. For each x, we must pick 1,2,...,x-1,x+1. Then, we can pick any x-2 numbers. There will be n-x-1 choose x-2 ways to do this. After choosing the numbers in the subarray, there are (2 * (x-1))! ways to permute this subarray. Then lets construct the permutation(the numbers outside the subarray). View the good subarray as a block. Then there are n - 2*(i+1) numbers/blocks left + this big block =  n - 2*(i+1) + 1 blocks. Thus, there are (n - 2*(i+1) + 1)! ways to permute the entire array such that the subarray is good.
To not get 60 points partial, we need to make sure everything doesnt go out of bound. Specifically dont let n- 2*(i-1)+1 < 0 occur. Thus its divided into even and odd cases.
```cpp
void solve(){
    int n;cin>>n;
    if(n%2==0){
        rep(i,1,n/2+1){
            cout<<mult(mult(nCk(n-i-1,i-2), fact[2*(i-1)]),fact[n-2*(i-1)+1])<<" ";
        }
        rep(i,n/2+1,n+1){
            cout<<0<<" ";
        }
    }
    else{
        rep(i,1,n/2+2){
            cout<<mult(mult(nCk(n-i-1,i-2), fact[2*(i-1)]),fact[n-2*(i-1)+1])<<" ";
        }
        rep(i,n/2+2,n+1){
            cout<<0<<" ";
        }
    }
    
    cout<<"\n";
}

// NOTE: IN THE MAIN FUNCTION, PRECOMPUTE FACTORIAL. WRITE: precompute(100005);
```
