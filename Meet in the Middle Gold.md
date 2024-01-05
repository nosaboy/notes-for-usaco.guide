## Usage
- Solve problems where constraint $n$ will cause TLE, but $\frac{n}{2}$ will AC.
  - Cases such as n = 40 will TLE O(2^n), but n/2 = 20 will not TLE: 2^20 = 1048576 operations
  - Allows us to have O($2^{\frac{n}{2}}$) complexity

 **Idea:** 
 1. Solve the problem for the first half of the input
 2. Solve the problem for the second half of the input
 3. Find a way to combine these two answer together to get the final answer


**Example 1:** https://cses.fi/problemset/task/1628/

If n = 20, the problem because easy since we can just brute force over all subsets in O(2^n).
The constraint is n = 40 which doesn't work, but $\frac{n}{2}$ will work. This calls for meet in the middle.
We solve the problem for the first half of the array, getting all subset sums for the first 20 integers.
Then we solve the problem for the second half of the array, getting all subset sums for the last 20 integers.
Then, we can merge these two answers together. For every sum in the first array, we can find all subsets that produces k-sum in the second array using binary search. We get ans by adding them together.
This divided our search in half.
A similar logic can be applied to solve **Sum of Four Values:** https://cses.fi/problemset/task/1642
```cpp
void solve(){
    int n,k; cin>>n>>k;
    vi a; vi b; // divide into two arrays
    map <ll,int> mp; // using map will lead to TLE
    rep(i,0,n/2){
        int u;cin>>u; a.pb(u);
    }
    rep(i,n/2,n){
        int u;cin>>u; b.pb(u);
    }
    vi left; vi right;
    rep(i,0,(1<<a.size())){
        ll sum = 0;
        rep(j,0,a.size()){
            if(i&(1<<j)){ // in subset
                sum += a[j];
            }
            if(sum > k || i < (1<<j)){
                break;
            }
        }
        left.pb(sum);
        // mp[sum]++; TLEs
    }
    ll ans = 0;
    rep(i,0,(1<<b.size())){
        ll sum = 0;
        rep(j,0,b.size()){
            if(i&(1<<j)){ // in subset
                sum += b[j];
            }
            if(sum > k || i < (1<<j)){
                break;
            }
        }
        right.pb(sum);
        // ans += mp[k-sum]; TLEs
    }
    sort(right.begin(),right.end());
    rep(i,0,left.size()){ // passes
        auto low_iterator = lower_bound(right.begin(), right.end(), k - left[i]);
        auto high_iterator = upper_bound(right.begin(), right.end(), k - left[i]);
        int start_index = low_iterator - right.begin();
        int end_index = high_iterator - right.begin();
        ans += end_index - start_index;
    }
    cout<<ans<<"\n";
}
```

**Things learned:** Always use vector + binary search since map has high constant factor that may lead to TLE.
