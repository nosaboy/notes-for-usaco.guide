## Usage
Uses the idea of brute force + greedy to divide and store problems into subproblems to solve original problem(like recursion).
- Find optimal solution
- Find number of solutions

Sometimes we can **write a slower brute forcy solution** in order to:
- get a better understanding on how to optimize dp & details
- earn partial credit
- generate stress tests

DP problems generally require two ideas:
- Base Case
- Transition Step

### Coin Problem: https://cses.fi/problemset/task/1634
#### Finding most optimal solution
Given sum n and set of coins c, find minimum number of coins needed to sum up to n.

We can solve the general case given any n and any set of c coins using dp in O(n). Lets find the base case and the recursive step. The base case is just $dp[0] = 0$ and $dp[c_i] = 1$ for every $c_i$. 

The inductive/transition step is just every sum x can be formed by $dp[x - c_i] + 1$ since we are using 1 move to get from $x-c_i$ to $x$ for every $c_i$. Thus, $dp[x] = min(dp[c_i])$ for all $i$. 

To use dp & memoization, we must define array $dp[n+1]$ and set every value at the start to inf. Then we do recursion. For each x, if $dp[x-c_i] \neq inf$, we just use $dp[x-c_i]$, else we calculate $dp[x-c_i]$ by doing recursion again.
```cpp
void solve(){
    int k,n; cin>>k>>n;
    vi c;
    rep(i,0,k){
        int u;cin>>u;c.pb(u);
    }
    int dp[n+1];
    rep(i,0,n+1){
        dp[i] = 1000000005; // set dp to INF
    }
    dp[0] = 0;
    rep(i,1,n+1){ // for every sum
        rep(j,0,k){ // for every coin
            if(i - c[j] >= 0){
                dp[i] = min(dp[i], dp[i-c[j]] + 1);
            }
        }
    }
    if(dp[n] == 1000000005){
        cout<<-1<<endl;
    }
    else{
        cout<<dp[n]<<endl;
    }
       
}

```
#### Constructing most optimal solution. 
To construct the optimal coin combinations for n, we want to store for each sum $x$ the last coin added. We can then get the last coin in the optimal solution, then subtract that coin from the sum to get the next coin used in the valid solution. 
```cpp
int last[n+1];
// add inside when looping through every coin for a sum
if(i - c[j] >= 0 && dp[i-c[j]] + 1 < dp[i]){
  dp[i] = dp[i-c[j]] + 1;
  last[i] = c[j]; // we used c[j] as our last coin
}
// add at the end
vi ans;
while(n > 0){
  ans.pb(last[n]); // push to answer
  n -= last[n]; // get the next coin
}
```
#### Counting number of solution
**If the solutions are not ordered:** https://cses.fi/problemset/task/1635
The number of solutions is just the sum of the number of solutions of its subproblems, since 2 + 3 is different from 3 + 2.
```cpp
void solve(){
    int k,n; cin>>k>>n;
    vi c;
    rep(i,0,k){
        int u;cin>>u;c.pb(u);
    }
    int dp[n+1];
    rep(i,0,n+1){
        dp[i] = 0; 
        // set dp to 0 since we didnt form anything at the start
    }
    dp[0] = 1; // you can construct sum of 0 in 1 way
    rep(i,1,n+1){ // for every sum
        rep(j,0,k){ // for every coin
            if(i - c[j] >= 0){
                dp[i] = add(dp[i], dp[i-c[j]]);
            }
        }
    }
   
    cout<<dp[n]<<endl;
    
       
}
```
**If the solutions ARE ordered:** https://cses.fi/problemset/task/1636/
We want to sort and construct solution in order. After we stop picking a coin, we cannot pick that coin again in the future(we visualize each coin value as a block). We loop through each coin THEN loop through each sum. This way we can first process the number of ways for specificly that type of coin. This will make the construction of the sum be in order.
```cpp
void solve(){
    int k,n; cin>>k>>n;
    vi c;
    rep(i,0,k){
        int u;cin>>u;c.pb(u);
    }
    int dp[n+1];
    rep(i,0,n+1){
        dp[i] = 0;
        
    }
    dp[0]=1;
    rep(i,0,k){ // for every coin
        rep(j,1,n+1){ // for every sum
            // by doing looping every coin first, we ensure that every coin stays together in one block when constructing sum
            if(j - c[i] >= 0){
                dp[j] = add(dp[j], dp[j-c[i]]); // j can be constructed using all previous coins c_0...c_i
                // by adding one c_i coin to j-c_i.
            }
        }
    }
    cout<<dp[n]<<endl;
       
}
```
**Problem 1:** https://codeforces.com/contest/1418/problem/C 
We can simulate the process using dp(kind of like frog but you need more stuff). 
```cpp
void solve(){
    int n;cin>>n; vi v; v.pb(0);
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    int dp[n+1][2];
    rep(i,0,n+1){
        dp[i][0] = 1000000005;
        dp[i][1] = 1000000005;
    }
    dp[0][0] = 0;
    rep(i,1,n+1){
        if(i > 1){
            dp[i][0] = min(dp[i][0],dp[i-2][1]);
            dp[i][0] = min(dp[i][0],dp[i-1][1]);
            dp[i][1] = min(dp[i][1],dp[i-2][0] + v[i] + v[i-1]);   
            dp[i][1] = min(dp[i][1],dp[i-1][0] + v[i]);   
        }
        else{
            dp[i][0] = min(dp[i][0],dp[i-1][1]);
            dp[i][1] = min(dp[i][1],dp[i-1][0] + v[i]);   
        }
    }
    cout<<min(dp[n][0],dp[n][1])<<endl;
}
```

**Problem 2:**

We note that this problem basically wants find a range that maximizes the count of u - count of k for some u. We can just fix what u is, then solve for when only u and x is in the array. We use the maximum subarray sum method to do this, sotring the min value of cnt_u - cnt_k in mn, then the maximum subarray that ends in $i$ is $cnt[u] - cnt[k] - mn$ when $u = v[i].$
```cpp
void solve(){
    int n,k; cin>>n>>k;
    vi v; 
    int pre[n+1]; // prefix of count of k
    map <int,int> mp; // we only visit each number once when placing initial mn
    map <int,pi> mn; // minimum of prefix sum(value of cnt[u] - cnt[k])
    map <int,int> cnt; // current count of number u
    pre[0]=0;
    int tot = 0;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        pre[i+1]=pre[i];
        if(mp[u]==0){
            mp[u]=1;
            // we initially set min to 
            mn[u]={-pre[i+1],i};
        }
        if(u == k){
            pre[i+1]++;
            tot++;
        }
    }
    int ans = 0;
    rep(i,0,n){
        int u = v[i]; // current number
        if(mn[u].first >= cnt[u] - cnt[k]){
            mn[u] = {cnt[u] - cnt[k], i+1};
        }
        cnt[u]++;        
        ans = max(ans, (cnt[u] - cnt[k]) - mn[u].first + tot);
    }
    cout<<ans<<endl;
}

```
