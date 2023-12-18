

**Problem 4:** https://codeforces.com/contest/1462/problem/E2

Time: 20 minutes

Self Editorial:
We note that after sorting the array, the min and max will just be the left and right bounds such that their difference is no more than k. We then note that we can choose any m people in this range. Let us fix the left bound by looping through it. We want the total distinct ways, so lets sum up all m tuples where v_i is the smallest in the tuple(curr left bound). We then use two pointers and increase until we reached a right bound(the max element such that v_r - v_i <= k). We first pick v_i into the tuple. We now have r - i choices and m-1 spots left. Thus we add (r-i choose m-1).
We precompute nCk using a template function.

```cpp
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
