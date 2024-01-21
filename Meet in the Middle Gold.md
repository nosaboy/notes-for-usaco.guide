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



**Problem 1:** https://codeforces.com/contest/888/problem/E

We see n = 35 which is suspicously small. However, we cant do 2^35. This is hint for meet in the middle.
The extra constraint is that first sum + second sum cannot exceed MOD. If it does, we can never go back to first sum again so its just better to take first sum initially. Thus, we want for every first sum the maximums second sum such that first + second < MOD. 
We calculate subset sum for first and second seperately using meet in the middle. Then we binary search for max and find max sum in total.
This is slick way to using meet in the middle to eliminate the exceeding MOD problem we have if we do it normally.

```cpp
void solve(){
    int n,m;cin>>n>>MOD;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    // subset mod sum for first half of array
    vi osa;
    rep(i,0,(1<<((n+1)/2))){
        int sum = 0;
        rep(j,0,(n+1)/2){
            if((i&(1<<j))){ // in subset
                sum = add(sum, v[j]);
            }
        }
        osa.pb(sum);
    }
    // subset mod sum for second half of array
    vi a;
    rep(i,(n+1)/2,n){
        a.pb(v[i]);
    }
    vi nosa;
    rep(i,0,(1<<(a.size()))){
        int sum = 0;
        rep(j,0,a.size()){
            if((i&(1<<j))){ // in subset
                sum = add(sum, a[j]);
            }
        }
        nosa.pb(sum);
    }
    int ans = 0;
    sort(nosa.begin(),nosa.end());
    rep(i,0,osa.size()){
        auto it = lower_bound(nosa.begin(),nosa.end(), MOD-osa[i]); // max mod sum that when summed up with osa it doesnt go over MOD.
        if(it != nosa.begin()){
            --it;
            ans = max(ans, *it + osa[i]); // update ans
        }
    }
    cout<<ans<<endl;
 
}   
```

**Problem 3:** https://codeforces.com/contest/1006/problem/F

We know that a path from (1,1) to (n,m) must contain n-1 down moves and m-1 right moves in some order for a total of 40 moves. Since they are both = 20 we can just meet in the middle by calculating the first 20 moves and the last 20 moves, then combining their XOR.

We brute force the first 20 paths, record each of their XORs at their ending coordinate and put it into a map which counts the number of paths ending at (x,y) with XOR sum = a.
Then, we brute force over last 20 paths. Then after going from (n,m) to some (x,y) we have XOR sum b, we'll add to ans the number of paths such that the XOR sum of the first 20 paths is k^b. This way, we have b^k^b = k. Thus the total path XOR is k.
This is simple brute force impl and shouldnt be 2100 but ok. Meet in the Middle cheese problems ig.

```cpp
ll n,m,k;
ll a[20][20];
map <tuple<ll,ll,ll>,ll> cnt; // coordiates (x,y), value
ll ans = 0;
void rec(int x, int y, int moves, ll osa){
    osa ^= a[x][y];
    if(moves == (n-1+m-1)/2){
        cnt[{x,y,osa}]++;
        return;
    }
    if(x+1 < n){
        rec(x+1,y,moves+1,osa);
    }
    if(y+1 < m){
        rec(x,y+1,moves+1,osa);
    }
}
void back(int x, int y, int moves, ll osa){

    if(moves == n-1+m-1-(n-1+m-1)/2){
        ans += cnt[{x,y,k^osa}];
        return;
    }
    if(x-1 >= 0){
        back(x-1,y,moves+1,osa^a[x][y]);
    }
    if(y-1 >= 0){
        back(x,y-1,moves+1,osa^a[x][y]);
    }
}
void solve(){
    cin>>n>>m>>k;
    
    rep(i,0,n){
        rep(j,0,m){
            cin>>a[i][j];
        }
    }

    rec(0,0,0,0);
    back(n-1,m-1,0,0);
    cout<<ans<<endl;
}   
```
