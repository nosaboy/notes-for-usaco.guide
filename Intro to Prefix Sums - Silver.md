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
http://www.usaco.org/index.php?page=viewproblem2&cpid=572
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

http://www.usaco.org/index.php?page=viewproblem2&cpid=595
If their IDs are a multiple of 7, it must mean that $(pre[r] - pre[l-1]) \equiv 0 \pmod{7}$ which means $pre[r] \equiv pre[l-1] \pmod{7}$
