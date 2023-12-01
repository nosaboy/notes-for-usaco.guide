### Usage
Create 2 pointers that iterates through the array in the same direction
We don't know its specific operation for every loop but we can estimate its complexity based on the total
number of operations: usually its O(n) since we always either increase left or right pointer
**Problem 1:** https://cses.fi/problemset/task/1640
We sort the array.
We maintain a two pointers starting with left at 0 and right at n-1. Moving the left pointer right increases the sum while moving the right pointer left decreases the sum. If current sum is smaller, we increase. If current sum is bigger we decrease. We do this until either we find the target sum or the pointers intersect meaning it is IMPOSSIBLE.
If the sum is smaller we 
```cpp
void solve(){
	int n,x; cin>>n>>x;
    vector <pi> v;
 
    rep(i,0,n){
        int u; cin>>u; v.pb({u,i+1}); 
    }
    sort(v.begin(),v.end());
    int l = 0;
    int r = n-1;
    while(l < r){
        if(v[l].first + v[r].first < x){
            l++;
        }
        else if(v[l].first + v[r].first > x){
            r--;
        }
        else{
            cout<<v[l].second<<" "<<v[r].second<<endl;
            return;
        }
    }
    cout<<"IMPOSSIBLE"<<endl;

}   
```

**Problem 2:** https://cses.fi/problemset/task/1660
Maintain 2 pointers that represents the left and right of a subarray. If the current subarray is bigger 
than our target x, we know that to make it smaller we must increment the left so the first number is not
counted. If our subarray sum is smaller, we must increment right since doing that will add on a new number
which will make sum bigger. We keep doing this at each step, keep increasing and decreasing until we find 
target. 
This is O(n) since for each operation, we must either increase lleft by 1 or increase right by 1. Thus there
are at most 2n operations until both left and right reaches index n.
```cpp
void solve(){
	int n,x; cin>>n>>x;
    vi v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    int l = 0;
    
    int sum = 0;
    int ans = 0;
    rep(r,0,n){ // we iterate through every r
        sum += v[r]; // we first increase sum since from previous loop we know
	//the sum is smaller or equal to target
        while(l < r && sum > x){
	// we decrease sum until the sum is smaller or equal to target       
            sum -= v[l];
            l++;
        }
        if(sum == x){ // count the number of subarrays
            ans++;
        }
    }
    cout<<ans<<endl;
 
}
```
