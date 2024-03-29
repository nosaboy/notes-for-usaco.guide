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

**Problem 11:** https://codeforces.com/problemset/problem/1606/E
After 30 minutes, no clue on how to start the problem
Hint 1: Let's calculate the following dynamic programming dp(i,j) — the number of ways to choose the initial health if there are i heroes still alive, and they already received j damage.

After thinking, my question is: If we do push DP, how would we transition states. From dp(i,j), we know the next damage done is j + i, but how do we keep track of i? 
Ok, so we iterate through k. But this wouldnt work, because everyone is different for each k?

nah I just have no clue how to do the transition I give up.

Ok I get it. We actually just do not care about the people who are dead at all. We instead just conquer the problem when we are at it.
Assume that currently, there are i people left alive and the total damage dealt is j. Now, we dont know what these i people are packing(health wise), all we know is that all of them have > j health. We have already previously calculated some DP which **erased all previous heros** with some arrangements.
Thus, we actually do not care about what the previous heros health was. All we know is that currently dp(i,j) stores the number of ways to choose n-i heros such that they have all died after j damage. However, this doesnt affect the health of any of the i heros remaining since we dont know their health yet and they havent died. **We can then construct the health of this current round from here**.

In this round, we can let any amount from 0 to k heros be alive after. After this, well push DP to k people alive and i-1+j damage dealt, since each hero gets i-1 damage dealt after the round. To get to our current position we had dp(i,j) choices to calculate the ways such that n-i people died after j damage was dealt. Then we just multiply this with the number of ways to choose i-k people to die after dealing damage i+j and push DP to the new k.

Now, we will not know what the health of these k people are cause we cant estimate it since they live on. However, we know what the health of the i-k people who will be dead are. This is because we would have dealt j+i-1 damage after this. Thus, these people must have health between j+1 to j+i-1. Of course, the max health is x so their health ranges from j+1 to min(j+i-1,x). We have i choose i-k choices to choose the i-k people who will die this round. For each of these people we have min(j+i-1,x) - j choices to choose the health such that they will die in this round since j+1 <= ai <= j+i-1. Since there are i-k people it total ways is (min(j+i-1,x) - j)^(i-k). Thus, the transition is 
$$DP[k][i+j] = \binom{i}{i-k} * (min(j+i-1,x) - j)^{i-k} * DP[i][j].$$
The base case is obviously we have n heros remaining, 0 damage dealt, and there is 1 way to arrange this which is just the start.
```cpp
void solve(){
    ll n,x;cin>>n>>x;
    precompute(505); // precompute n choose k
    ll dp[505][505]; // max is 505
    memset(dp,0LL,sizeof(dp)); 
    
    dp[n][0] = 1LL; // base case
 
    
    for(ll i = n;i>1;i--){ // calculate from n people left first(max ppl to min)
        for(ll j = 0;j<x;j++){ // calculate from 0 damage done to x damage done(min dmg to max)
            
            ll ok = (min(j+i-1,x) - j); 
            ll prod = 1;
            for(ll k = i;k>=0;k--){
                dp[k][min(x,i-1+j)] = add(dp[k][min(x,i-1+j)],mult(nCk(i,i-k), mult(prod, dp[i][j]))); // formula
                prod = mult(prod, ok); // for num_of_possible_a[i]^(i-k), calculate power fast
                
                
            }
        }
    }
    ll ans = 0;
    rep(i,0,505){
        ans = add(ans, dp[0][i]);
    }
    cout<<ans<<"\n";
}
```

**Problem 13:** https://codeforces.com/problemset/problem/1666/F

This is prob dp of some sort
Hint 1(I knew this was coming): Solve the problem with no repetition values of a_i.
Go from largest to smallest value. We visualize the positions as being initially empty.
Define $dp_{i,j}$ as filling $j$ values to even positions from the first $i$ values from largest to smallest. Then, the transition from i to i+1 will be if the i+1th value gets picked as even or odd.
If we set the i+1 value as even, we must place it at the next avaliable even spot. Because we are going from largest to smallest value and b_i must decrease, so if its not at the next even spot then that spot will have a smaller value than curr. So there is only 1 way to place it to the next even spot, so # of ways is just $dp[i+1][j+1] = dp[i][j]$. 
If we set i+1 value as odd, we note that the number of odd spots possible must be j-1 (+1 if j == 2 since we must place a number in 1st position when we reach end). This is because there are currently j spots occupied, and we must place this value between two even values that are larger. If we place it at the front, the next even value will be smaller(cause we are going from large to small) so sequence is invalid. Then, since there are already i-j values in these j-1 spots we must not place it in any of these spots. Thus, it has max(0,j-1)-(i-j)-(i==n?). Since each placement is weighted the same(doesnt matter which spot we pick the ways will always be multipled by $dp[i][j]$), we have in total $dp[i+1][j] = (max(0,j-1)-(i-j)-(i==n?)) * dp[i][j]$ ways.

Now we consider if there are repetition of values. For each value, we will process everything all at once with similar transition:
- All elements that have same value goes to odd positions
- One element will go to the next even value and all other element goes to odd positions
Thus, we essentially just do the old dp transitions, but we process same elements at the same time. That is, if all values go to odd position there are n = (max(0,j-1)-(i-j)-(i==n?)) positions and k = cnt elements with this value so its nCk.
If one element is at even spot, there n = (max(0,j-1)-(i-j)-(i==n?)) positions and k = cnt elements with this value but we minus 1 since one is at a even spot so its nC(k-1).

**Relfection**
This was rlly hard and I had to consult editorial several times. Probably wouldve not solved in contest.

```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
	int dp[n+1][n+1];
	memset(dp,0,sizeof(dp));
	sort(v.rbegin(),v.rend());
    v.pb(-1);
    vector<pi> a;
    int cnt = 1;
    rep(i,0,n){
        if(v[i]==v[i+1]){
            cnt++;
        }
        else{
            a.pb({v[i],cnt});
            cnt= 1;
        }
    }
    int sz=0;
    dp[0][0]=1;
    rep(i,0,a.size()){
        rep(j,0,n+1){
            if(j<=n/2){ // can add to it
                dp[sz+a[i].second][j] =add( dp[sz+a[i].second][j], mult(nCk(max(0,j-1)-(sz-j)+(j==n/2),a[i].second), dp[sz][j]));
            }
            if(j+1<=n/2){ // can add to it
                /*
                We place curr value as the next even position. There will be max(0,j-1) + (j==n/2) odd positions such that the sequence is valid. However, sz-j of these spots are already placed so we subtract that. That is the total number of choices. We then choose a[i]-1 of these spots and fill it with our curr value. We then multiply all that by the previous number of ways which is just dp[sz][j].
                */
                dp[sz+a[i].second][j+1] = add(dp[sz+a[i].second][j+1],mult(nCk(max(0,j-1)-(sz-j)+(j==n/2),a[i].second-1) , dp[sz][j]));
            }
            
           
        }
        sz += a[i].second;
    }
    cout<<dp[n][n/2]<<endl;
}   
```


