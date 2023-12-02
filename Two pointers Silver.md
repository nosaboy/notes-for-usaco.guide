### Usage
Create 2 pointers that iterates through the array in the same direction
We don't know its specific operation for every loop but we can estimate its complexity based on the total
number of operations: usually its O(n) since we always either increase left or right pointer
#### CF ITMO Pilot
**Merge two sorted arrays into one big array**
https://codeforces.com/edu/course/2/lesson/9/1/practice/contest/307092/problem/B

We can maintain two pointers that begin at each array. We know that during every iteration, the next smallest number will be one of the two values at the front of the array. We can then insert this value then go on to the next iteration by increasing that pointer by 1. Since in every iteration we increase a pointer by 1, there is at most 2n iterations so time complexity is O(n).
```cpp
void solve(){
	// merge sorted array a length and sorted array b length m to array c
    int n,m; cin>>n>>m;
    vi a; vi b; vi c;
    rep(i,0,n){ // sorted
        int u; cin>>u; a.pb(u);
    }
    rep(i,0,m){ // sorted
        int u; cin>>u; b.pb(u);
    }
    int i = 0; int j = 0; // two pointers
    while(i < a.size() || j < b.size()){
        if(j == b.size() || i < a.size() && a[i] < b[j]){
            // it is more optimal to pick a if we already picked
            // all elements in b or ai < bi and we havent picked
            // all elements in a
            c.pb(a[i]); i++;
        }   
        else{
            c.pb(b[j]); j++;
        }

    }
    rep(i,0,c.size()){
        cout<<c[i]<<" ";
    }

}   
```
**Given array a and b, for each element in b:(b_i), find how many elements in a is smaller than b_i.**
https://codeforces.com/edu/course/2/lesson/9/1/practice/contest/307092/problem/A

Lets say a have k elements smaller than b_i, then all those k elements will be smaller than b_{i+1}. Lets maintain two-pointers **assuming a and b are sorted**. For each b_i we will go through a_j until a_j > b_i. This j will be the number of a_j smaller than b_i.
```cpp
void solve(){
    int n,m; cin>>n>>m;
    vi a; vi b; vi c;
    rep(i,0,n){ // sorted
        int u; cin>>u; a.pb(u);
    }
    rep(i,0,m){ // sorted
        int u; cin>>u; b.pb(u);
    }
    int ans[m];
    int j = 0;
    rep(i,0,m){
        while(a[j] < b[i] && j < n){
            j++;
        }
        ans[i] = j; // 0th index so there is exactly j elements that is smaller
    }
    rep(i,0,m){
        cout<<ans[i]<<" ";
    }

}   
```
**Example 1:** https://codeforces.com/contest/279/problem/B
Keep a two-pointer the represents the sum of the current subarray. As we add the next value by right++, we only subtract values if we have to, meaning we subtract until we have sum <= t again, then we just print max(right - left + 1).
```cpp
void solve(){
	int n,t; cin>>n>>t;
    vi v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    int l = 0;
    int sum = 0;
    int ans = 0;
    rep(r,0,n){
        sum += v[r];
        while(l < r && sum > t){
            sum -= v[l]; l++;
            
        }
        if(sum <= t){
            ans = max(ans, r- l + 1);
        }
    }
    cout<<ans<<endl;

}   
```
**Problem 1:** https://cses.fi/problemset/task/1640
We sort the array.
We maintain a two-pointers starting with left at 0 and right at n-1. Moving the left pointer right increases the sum while moving the right pointer left decreases the sum. If current sum is smaller, we increase. If current sum is bigger we decrease. We do this until either we find the target sum or the pointers intersect meaning it is IMPOSSIBLE.
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

**Problem 3:** https://cses.fi/problemset/task/1641
This is very similar to the 2 sum problem but now there are 3 values that sum up to target and O(n^2) can pass. To go from 2 sum to 3 sum, we can iterate through every point and fix the middle value, then perform 2 sums on the remaining point. If left pointer goes over middle value or right pointer goes before middle value, we break since this means we didn't find a solution. Otherwise we use the same logic as 2 sums where we keep track of the sum and see if its bigger or smaller. Basically just 2 sums for every middle value.
```cpp
void solve(){
	int n,x; cin>>n>>x;
    vector <pi> v;
 
    rep(i,0,n){
        int u; cin>>u; v.pb({u,i+1}); 
    }
    sort(v.begin(),v.end());
    rep(i,0,n){
        int l = 0;
        int r = n-1;
        while(l < i && i < r){
            if(v[i].first + v[l].first + v[r].first < x){
                l++;
            }
            else if(v[i].first + v[l].first + v[r].first > x){
                r--;
            }
            else if(i != l && i != r){
                cout<<v[i].second<<" "<<v[l].second<<" "<<v[r].second<<endl;
                return;
            }
            else{
                break;
            }
        }
    }
    
    cout<<"IMPOSSIBLE"<<endl;

}   
```
