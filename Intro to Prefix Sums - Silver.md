# Introduction to Prefix Sums
### Usage
Given integer array a, query the sum of any range(l,r) [l...r] 
precomputation: O(n)
processing: O(1)


### Example 1

https://judge.yosupo.jp/problem/static_range_sum
- Basic prefix sum implementation
- Precompute all prefix of sums
- The sum of range [l,r] inclusive will be pre[r] - pre[l-1]

```cpp
void solve(){
    int n;
    cin>>n;
    int q;
    cin>>q;
    ll a[n];
    rep(i,0,n){
        cin>>a[i];
    }
    ll pre[n+1];
    pre[0] = 0; 
    rep(i,1,n+1){
        pre[i] = a[i-1] + pre[i-1];
    }
    
    while(q--){
        int l,r;
        cin>>l>>r; l++;
        cout<<pre[r] - pre[l-1]<<endl;
    }
}
```

### Problems
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=572
We can just keep track of the sum of the number of cows for each ID in a certain interval using prefix sums. 
For every cow at $a[i]$, we will add 1 to the prefix sum
```cpp
void solve(){
    int n;
    cin>>n;
    int q;
    cin>>q;
    int pre[3][n+1]; //ID, index
    pre[0][0] = 0;
    pre[1][0] = 0;
    pre[2][0] = 0;
    
    rep(i,0,n){
        pre[0][i+1] = pre[0][i];
        pre[1][i+1] = pre[1][i];
        pre[2][i+1] = pre[2][i];
        int u; cin>>u; u--;
        pre[u][i+1]++;
    }

    
    while(q--){
        int l,r;
        cin>>l>>r; 
        cout<<pre[0][r] - pre[0][l-1]<<" "<<pre[1][r] - pre[1][l-1]<<" "<<pre[2][r] - pre[2][l-1]<<endl;
    }
}
```

**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=595
If their IDs are a multiple of 7, it must mean that $pre[r] - pre[l-1] \equiv 0 \pmod{7}$ which means $pre[r] \equiv pre[l-1] \pmod{7}$
To find the max, we just need to find the maximum length for each difference from 0 to 6 which means finding the min and max index such that their prefix have the same mod 7.
```cpp
void solve(){
    int n;
    cin>>n;
    ll pre[n+1];
    pre[0] = 0;
    rep(i,0,n){
        int u;
        cin>>u;
        pre[i+1] = pre[i] + u;

    }
    vi v[7];
    rep(i,0,n+1){
        v[pre[i]%7].pb(i);
    }
    int ans = 0;
    rep(i,0,7){
        sort(v[i].begin(),v[i].end());
        if(v[i].size()){
            ans = max(ans, v[i][v[i].size()-1] - v[i][0]);
        }
        
    }
    cout<<ans<<endl;
}

```
**Problem 3:** http://www.usaco.org/index.php?page=viewproblem2&cpid=691
We can just check all cases. We first maintain a prefix sum that stores how many each move is used by Farmer John. We then check all 3 options for Bessie, then for each choice we loop over and fix our switching point. After switching we just pick the max option that farmer john picked since we know we can always counter it.
```cpp
void solve(){
    int n;
    cin>>n;
    int pre[3][n+1];
    pre[0][0] = 0;
    pre[1][0] = 0;
    pre[2][0] = 0;

    rep(i,0,n){
        char c; cin>>c;
        pre[0][i+1] = pre[0][i];
        pre[1][i+1] = pre[1][i];
        pre[2][i+1] = pre[2][i];
        if(c== 'H'){
            pre[0][i+1]++;
        }
        if(c== 'P'){
            pre[1][i+1]++;
        }
        if(c== 'S'){
            pre[2][i+1]++;
        }
    }
    int ans = 0;
    rep(i,0,3){ //check over all possible combinations
        rep(j,0,3){
            rep(l,1,n+1){ // fixed switching point
                ans = max(ans, pre[i][l] - pre[i][0] + pre[j][n] - pre[j][l]);
            }
        }
    }
    cout<<ans<<endl;
    
}
```
**Problem 4:** https://cses.fi/problemset/task/1661
Lets fix the ending point of some subarray by looping each index from 0 to n-1. If $pre[i] = a$ for some a, in order for the final sum of a subarray $[l,i]$ to be x we must have $pre[l-1] = a-x.$ We maintain a map which counts every previous prefix such that $pre[l-1] = a-x$ so $mp[a-x]$ is the number of prefixes = $a-x.$ Thus, the number of valid subarrays ending in i is just $mp[a-x].$

```cpp
void solve(){
    int n;
    ll x;
    cin>>n>>x;
    ll pre[n+1];
    pre[0] = 0;
    map <ll,ll> mp;
    mp[0] = 1;
    ll ans = 0;
    rep(i,0,n){
        ll u;
        cin>>u;
        pre[i+1] = pre[i] + u;
        ans += mp[pre[i+1] - x];
        // we do this after since we might count pre[r] - pre[r] = 0 which
        // is just an empty subarray which is not valid
        mp[pre[i+1]]++;
    }
    cout<<ans<<endl;
    
}
```


