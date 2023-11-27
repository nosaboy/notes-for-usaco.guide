### Maximum Subarray Sums
Find the maximum sum over all subarrays(there may be negative values) 
- O(n) precomputing
- O(1) query
#### Example 1 
https://cses.fi/problemset/task/1643
Sum of subarray $[l,r] = pre[r] - pre[l-1].$ The maximum subarray sum that ends with r is $pre[r] - pre[l-1]$ where $pre[l-1]$ is minimized. We keep track of the minimum left $pre[l-1]$ and iterate through each $r$ and find the max of $pre[r] - mn.$
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
    ll mn = 0; //minimum is set to pre[0] = 0 at the start
    ll mx = -1000000005;
    rep(i,1,n+1){
        mx = max(mx, pre[i] - mn);
        mn = min(mn, pre[i]);
    }
    cout<<mx<<endl;
}
```

### 2D Prefix Sums
Find the sum of 
- O(nm) precomputing
- O(1) query
#### Example 1
https://cses.fi/problemset/task/1652

