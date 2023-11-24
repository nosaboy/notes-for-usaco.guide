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


```
