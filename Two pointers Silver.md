# Usage
Create 2 pointers that iterates through the array in the same direction
We don't know its specific operation for every loop but we can estimate its complexity based on the total
number of operations: usually its O(n) since we always either increase left or right pointer
## CF ITMO Pilot
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

**Example 3:** https://codeforces.com/edu/course/2/lesson/9/1/practice/contest/307092/problem/C
Find number of pairs (i,j) such that ai = aj
Not intended: Use map and set and just multiply and add.

We can two pointer through and increase whichever one is smaller. If they are the same 
## Subarray/segment of "good" sums
- Calculating the max/min length of segements/subarray that satisfy some condition
- Caculate the number of segments/subarrays that satisfy some condition

**Use Two Pointers if these two properties are satisfied:**
**Property 1:** one of the following is met:
- if the segment $[L,R]$ is good, then any segment nested in it is also good
	- If l...r is good, then l'...r' is good where l <= l' <= r' <= r.
	- **First Example:** If subarray works, smaller subarray within subarray also works
- if the segment $[L,R]$ is good, then any segment that contains it is also good
	- If l...r is good, then l'...r' is good where l' <= l <= r <= r'.
	- **Second Example:** If smaller subarray works, bigger subarray including smaller subarray also works
**Property 2:**
We can recalculate function we are checking (check if current segment is good or bad) in fast enough time like in O(1), while moving the left or right border by one to the right.

The code for two pointers will then look something like:
```cpp
L = 0
for R = 0..n-1
    add(a[R])
    while not good():
        remove(a[L])
        L++
```
where we have to implement the add(element), remove(element), and good() = check function. 

**Given array v of nonnegative integers and integer x, find the longest segment such that sum of segments is at most x.**
https://codeforces.com/edu/course/2/lesson/9/2/practice/contest/307093/problem/A

This is similar to the subarray sum problem(Problem 1). We can loop the right border and keep deleting the left elements until the sum is smaller than x, in which case we do ans = max(ans, r - l + 1) and increment right border again.
```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    int l = 0;
    
    ll sum = 0;
    int ans = -1;
    rep(r,0,n){ // we iterate through every r
        sum += v[r]; // we first increase sum since from previous loop we know
	//the sum is smaller or equal to target
        while(l < r && sum > x){
	// we decrease sum until the sum is smaller than target       
            sum -= v[l];
            l++;
        }
        if(sum <= x){ // count the length if sum is AT MOST target
            ans= max(ans, r - l + 1);
        }
    }
    if(ans == -1){ // didnt find subarray
        cout<<0<<endl;
        return;
    }
    cout<<ans<<endl;
 
}
```
**Given array v of nonnegative integers and integer x, find the shortest segment such that sum of segments is at least x.**
https://codeforces.com/edu/course/2/lesson/9/2/practice/contest/307093/problem/B

We use similar approach as above, except now we want to delete as much as possible for every right border. We first check if its possible to delete a left element, we then delete it and update the sum. We stop if deleting the left element results in the sum being smaller than x.

```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    int l = 0;
    
    ll sum = 0;
    int ans = 1000000005;
    rep(r,0,n){ // we iterate through every r
        sum += v[r]; // we first increase sum since from previous loop we know
	//the sum is smaller or equal to target
        while(l < r && sum - v[l] >= x){   
            sum -= v[l];
            l++;
        }
        if(sum >= x){ // count the length if sum is AT MOST target
            ans= min(ans, r - l + 1);
        }
    }
    if(ans == 1000000005){ // didnt find subarray
        cout<<-1<<endl;
        return;
    }
    cout<<ans<<endl;
 
}
```
**Given array v of nonnegative integers and integer x, find the NUMBER of segment such that sum of segments is at most x.**

We have to find number of segments instead of max/min length segments. This is similar. After fixing the right border, we can note that if the maximum length that ends at right border is l, then all other left borders > l and smaller than r works since we must only decrease the sum as we decrease the length meaning it will not get bigger. Thus we just add r - l to our answer since its the number of choices of the left border for every right border r. Here we defined that our current l doesn't work so we add 
r - l + 1.

```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    int l = 0;
    
    ll sum = 0;
    ll ans = 0;
    rep(r,0,n){ // we iterate through every r
        sum += v[r]; // we first increase sum since from previous loop we know
	//the sum is smaller or equal to target
        while(l < r && sum > x){
	// we decrease sum until the sum is smaller than target       
            sum -= v[l];
            l++;
        }
        if(sum <= x){ // count the length if sum is AT MOST target
            ans+=r-(l-1); // l-1 since the current l is the first l that goes over the sum
        }
    }
    cout<<ans<<endl;
 
}
```
**Given array v of nonnegative integers and integer x, find the NUMBER of segment such that sum of segments is at least x.**

This is the same thing as above where we count length. We fix border r and find the minimum length segment such that l...r >= x. We then know that if we increase the length, it will still be bigger, so the count for all good subarrays that end with r is just l+1 since all left borders from 0 to l work( here we defined that l itself works).
```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    ll l = 0;
    
    ll sum = 0;
    ll ans = 0;
    rep(r,0,n){ // we iterate through every r
        sum += v[r]; // we first increase sum since from previous loop we know
	//the sum is smaller or equal to target
        while(l < r && sum - v[l] >= x){   
            sum -= v[l];
            l++;
        }
        if(sum >= x){ // count the length if sum is AT MOST target
            ans+= l+1;
        }
    }
 
    cout<<ans<<endl;
 
}
```

**Given array of integers, find number of subarray such that number of unique elements is <= x.**
Check properties to confirm that we can use two pointers:
First property: If subarray l...r has <= x distinct values, then l'...r' where l <= l' <= r' <= r must also have <= k distinct values since its distinct values cannot go over the distinct values of l...r, thus first property is valid.
Second property: Can we calculate the good() function fast?
We can count the number of x in our current subarray. Then, if the count reaches 0 after deletion the number of distinct values decreases by 1, and if count increases by 1 after insertion and it was 0 before, the number of distinct values increases by 1. This can all be done using map in O(logn). We then check if number of distinct values is <= k, then only increment left if we are forced(distinct values > k). Then calculate max length or number of subarrays using previous logic. 

good(): return distinct <= k;
add(x): if mp$[x] == 0$ distinct++;
delete(x): if mp$[x] == 1$ distinct--;

**NOTE:** To solve this problem in O(n), we can switch the map for array.

```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    ll l = 0;
    
    ll ans = 0;
    map <int,int> mp;
    int distinct = 0;
    rep(r,0,n){ 
        if(mp[v[r]] == 0){ // add()
            distinct++;
        }
        mp[v[r]]++;
        while(l < r && distinct > x){ // good()
            // delete()
            mp[v[l]]--; 
            if(mp[v[l]] == 0){
                distinct--;
            }
            l++;
        }
        if(distinct <= x){ // check if valid subarray
            ans += r - l + 1;
        }
    }
 
    cout<<ans<<endl;
 
}
```

**Given array, subarray is good if max element - min element of subarray <= x. Find number of good subarray.**

First property: If subarray l...r satisfies, then l'...r' where l <= l' <= r' <= r satisfies since the min will only get larger and max will only get smaller, so difference can only get smaller.
Second property: We can keep track of min and max using multiset, then insert and erase accordingly. We then find max - min and only increment left if we are forced to: when max 0 min > x. 

```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    ll l = 0;
    
    ll ans = 0;
    multiset <ll> ms;
    rep(r,0,n){ 
        ms.insert(v[r]); // add()
        auto it = ms.end(); --it;
        while(l < r && *it - *ms.begin() > x){ // good()
            // delete()
            ms.erase(ms.find(v[l]));
            l++;
            it = ms.end(); --it; // currently the biggest element
        }
        if(*it - *ms.begin() <= x){ // check if valid subarray
            ans += r - l + 1;
        }
    }

    cout<<ans<<endl;
 
}
```
### Two Stacks Method
**NOTE:** This can also be solved in O(n) using stacks. We maintain two stacks that represent some arbitrary left and right region of our subarray. We also maintain max and min stacks for each left and right stack that contains our original values, and calculate their "prefix max and min."

Lets have positions 1 2 3 4 5 6 7 8 9 10, and let left stack contain 2 3 4, right stack contain 9 8 7 6 5 **From top to bottom order**. The top of left stack is the left most value. The top of right stack is the right most value. Then,
Left max = 4 4 4
Left min = 4 3 2
Right max = 9 8 7 6 5
Right min = 5 5 5 5 5

Good()
To calculate the min and max of subarray, we take the max and min value of both arrays, accessing them from the top of the prefix stack: max(rightmax.top(), leftmax.top()), min(rightmin.top(), leftmin.top())

Add()
To add some value from the right, we just add it to the top of the right stack since we are adding the right most element, so after adding 10 to the right stack we have
Right stack = 10 9 8 7 6 5
Right max = 10 9 8 7 6 5
Right min = 5 5 5 5 5 5

Delete()
To delete a value from the left side we delete the top element from the left stack since we are deleting the left most element. If the left stack is empty, we still want to delete the left most value so we transfer all elements in the right stack to the left stack and recalculate all min and max values. To transfer, we want to push the top of the second stack one by one. This works because the top of second stack is the right most element so pushing it to the left stack first means that it will be at the bottom. We pushed the left most element of the right stack last meaning that it will go on top of the left stack, this means that everything in the left stack is still in order where top = more left element. Then update each min and max prefix stack accordingly.
**Alternative solution using two stacks in O(n), faster time:**
```cpp
void solve(){
	ll n,x; cin>>n>>x;
    vector<ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    ll l = 0;
    
    ll ans = 0;
    stack <ll> left; stack <ll> leftmax; stack <ll> leftmin;
    stack <ll> right; stack <ll> rightmax; stack <ll> rightmin;
 
    rep(r,0,n){ 
        // add()
        right.push(v[r]); 
        if(rightmax.size()){
            rightmax.push(max(rightmax.top(), v[r]));
            rightmin.push(min(rightmin.top(), v[r]));
        }
        else{
            rightmax.push(v[r]);
            rightmin.push(v[r]);
        }
        // setting max and min value of two arrays
        ll mxval = 0;
        if(leftmax.size()){
            mxval = max(mxval, leftmax.top());
        }
        if(rightmax.size()){
            mxval = max(mxval, rightmax.top());
        }
        ll mnval = 1000000000000000005;
        if(leftmin.size()){
            mnval = min(mnval, leftmin.top());
        }
        if(rightmin.size()){
            mnval = min(mnval, rightmin.top());
        }
 
 
        while(l < r && mxval - mnval > x){ // good()
            // delete()
            l++;
                
           
            if(left.size()==0){ // we must get elements from right stack
                ll mn = 1000000000000000005; ll mx = 0;
                while(right.size()){
                    
                    mn = min(mn, right.top());
                    mx = max(mx, right.top());
                    left.push(right.top()); leftmax.push(mx); leftmin.push(mn);
                    right.pop(); rightmax.pop(); rightmin.pop();
 
                }
            }
            left.pop();
            leftmax.pop();
            leftmin.pop();
            mxval = 0;
            if(leftmax.size()){
                mxval = max(mxval, leftmax.top());
            }
            if(rightmax.size()){
                mxval = max(mxval, rightmax.top());
            }
            mnval = 1000000000000000005;
            if(leftmin.size()){
                mnval = min(mnval, leftmin.top());
            }
            if(rightmin.size()){
                mnval = min(mnval, rightmin.top());
            }
        }
        //cout<<left.size()<<" "<<right.size()<<" "<<l<<" "<<r<<" "<<mxval<<" "<<mnval<<endl;
        if(mxval - mnval <= x){ // check if valid subarray
            ans += r - l + 1;
        }
    }
 
    cout<<ans<<endl;
 
}

```
**Find the minimum length segment whose gcd is 1 and print -1 if there are no such segments.**
https://codeforces.com/edu/course/2/lesson/9/2/practice/contest/307093/problem/G

First, we know that the longer the subarray, the more chance it will be gcd = 1. Since we are finding the minimum length segment, for every l we will stop at the first time gcd hits 1, then go to the next l. All thats left is to calculate gcd of range efficiently. We can do this using segtree fo gcd queries.
```cpp
// SEGTREE FOR GCD QUERIES
struct segtree{
    int sz;
    vector <ll> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,0LL); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <ll> &a){
        build(a,0,0,sz);
    }
    void build(vector <ll> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                sum[x] = a[lx];
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        sum[x] = __gcd(sum[2*x+1], sum[2*x+2]);
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            sum[x] = u;
            return;
        }
        int m = (lx+rx)/2; // mid
        if(i < m){ // if desired position < mid, we go to left subtree
            set(i, u, 2*x+1,lx,m);
        }
        else{ // we go to right subtree
            set(i, u, 2*x+2,m,rx);
        }
        // recalculate current node sum after recursion
        sum[x] = __gcd(sum[2*x+1], sum[2*x+2]);
    }
    ll query(int l, int r){
        return query(l,r,0,0,sz);
    }
    ll query(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return 0;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        ll osa = query(l,r,2*x+1,lx,m);
        ll nosa = query(l,r,2*x+2,m,rx);
        return __gcd(osa,nosa);
    }
 
};


void solve(){
    int n;cin>>n;
    vector <ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
    }
    segtree st;
    st.init(n+1);
    st.build(v);
    int ans = 1000000005;
    int r = 0;
    rep(l,0,n){
        r = max(r, l);
        while(r < n && st.query(l,r+1) > 1LL){
            r++;
        }
        if(st.query(l,r+1) == 1LL){
            ans = min(ans, r-l+1);
        }
        
    }
    if(ans == 1000000005){ // didnt find segment
        cout<<-1<<endl;
        return;
    }
    cout<<ans<<endl;
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
**Problem 4:** http://www.usaco.org/index.php?page=viewproblem2&cpid=738
We are basically asked to minimize the maximum sum of pairings, so the optimal pairing of cows is to always pair the most possible at the moment with the least possible. We first sort cows by their value, then create two pointers where we pair the least value with the most value, and subtract the number of cows of that value accordingly.
```cpp
void solve(){
    int n; cin>>n;
    int l = 0;
    int r = n-1;
    vector <pi> v;
    rep(i,0,n){
        ll x,y; cin>>x>>y;
        v.pb({y,x});
    }
    sort(v.begin(),v.end());
    int ans = 0;
    while(l <= r){
        if(v[l].second < v[r].second){
            v[r].second -= v[l].second;
            ans = max(ans, v[l].first + v[r].first);
            l++;
        }
        else if(v[l].second > v[r].second){
            v[l].second -= v[r].second;
            ans = max(ans, v[l].first + v[r].first);
            r--;
        }
        else{
            ans = max(ans, v[l].first + v[r].first);
            l++; r--;
        }
    }
    cout<<ans<<endl;
}
```

**Problem 6:** https://codeforces.com/problemset/problem/701/C
This looks like the number of distinct values subarray problem, we can prove we can solve using two pointers.
First property: if l...r has all distinct characters, then l'...r' where l' <= l <= r <= r' has all distinct characters, so the second condition is satisfied.
Second property: We can keep track of the number of distinct characters by maintaining a counter like previously, then if it matches the total distinct elements we calculate ans, else we want to increase and decrease our coverage and subtract/add to our counter accordingly.
```cpp
void solve(){
	int n; cin>>n;
    string s;
    cin>>s;
    set <char> st;
    rep(i,0,n){
        st.insert(s[i]);
    }
    int types = st.size(); // # of distinct characters
    map <char,int> mp;
    int cnt = 0;
    int l = 0;
    int ans = 1000000000;
    rep(r,0,n){
        if(mp[s[r]] == 0){ // add()
            cnt++;
        }
        mp[s[r]]++;
       
        while(l < r && ((mp[s[l]] == 1 && cnt > types) || (mp[s[l]] > 1 && cnt >= types))){ // if we can decrease length we will
            // delete()
            mp[s[l]]--;
            if(mp[s[l]] == 0){
                cnt--;
            }
            
            l++;
        }
 
        if(cnt == types){
            ans = min(ans, r - l + 1);
        }
    }
    cout<<ans<<endl;
 
}
```

**Problem 8:** http://www.usaco.org/index.php?page=viewproblem2&cpid=643
Lets first calculate for one box, this is a simple two pointers where we calculate the maximum length for each r such that its values differ no more by k, where we sort the array.
Then, lets store the prefix maximum of each r, so $pre[r]$ is the maximum length "good" subarray that is to the left of r. Then iterate for every r we set the maximum lengthed good l...r as the first box, then set the second box as the maximum subarray to the left of l so $pre[l-1]$, then ans will be the max of the sum of the two lengths. This works because if we dont choose the left most l such that subarray l...r is good, the answer will only get worse.
```cpp
void solve(){
	int n,k; cin>>n>>k; vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end()); int ans = 0;
    int l = 0;
    pi pre[n+1];
    pre[0] = {0,0};
    rep(r,0,n){
        while(l < r && v[r] - v[l] > k){
            l++;
        }
        pre[r+1] = {max(pre[r].first, r - l + 1), l+1};

        
    }
    rep(i,1,n+1){
   
        ans = max(ans, i - pre[i].second + 1 + pre[pre[i].second-1].first );
    }
    cout<<ans<<endl;

}
```

**Problem 9:**
We take care of each case seperately. 
For the minimum number of moves, lets fix any interval length n as our final. Then the min moves to fill the intervals is the number of 0s in that interval. If the two borders are in the intveral we are done, else we can always place the border in one of the zeros and repeat this checking process again. There is an exception to this is when there is a block of cows and an extra cow somewhere else, the algo will count this as n - (n-1) = 1 moves when in reality you cant move the extra cow anywhere, thus we must use 2 moves to do this. Every other case we can always move one of the borders into the range. 

For the max, we realize that we can move the first border of the first move as far as possible, like one more inside the next cow, so we move cow 0 to position of cow 1 + 1 to maximize coverage. We can keep filling 0s in front of it and make a chain as we go, stretching our moves. Thus the answer is the max of the number of 0s in between cow 1 and cow n-1 or number of 0s between cow 0 and cow n-2, since we move cow 0 or cow n-1 on the first move, and move it to some 0 inside the allowed range.

```cpp
void solve(){
	int n;cin>>n; vi v;
    rep(i,0,n){
        int u;cin>>u; v.pb(u);
    }
    sort(v.begin(),v.end());
    int l = 0; int ans = 0;
    rep(r,0,n){
        while(l < r && v[r] - v[l] + 1 > n){
            l++;
        }
        if(v[r] - v[l] + 1 <= n){
            if(r - l + 1 == n-1 && (v[r] - v[l] + 1 != n)){ // special case where
            // all cows except for one cow are block 
            // and we have one cow outside of that block
                ans = max(ans, n-2);
            }
            else{
                ans = max(ans, r - l + 1);
            }
            
        }
    }
    cout<<n - ans<<endl;
    cout<<max(v[v.size() - 1] - v[1] + 1 - n + 1, v[v.size() - 2] - v[0] + 1 - n + 1);
}
```

# Sliding Window
A constant sized subarray(window) that moves(slides) through the array. We want to compute some information within each window.
Sliding Window Problems requires us to transition between windows and calculate how that affects the answer. Meaning we shift window by 1 by l++ and r++: subtract the left element and add the next right element.
**Example 1:** https://leetcode.com/problems/sliding-window-maximum/

Return the maximum value for each sliding window

We want to know how transitioning from 1 window to the next will affect current answer(max element). For each transition, we delete the previous element at left border(l) and add the new element on the right(r+1). We then calculate max of all elements. 
We can use multiset to do these 3 operations:
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        multiset <int> ms;
        vector <int> ans;
        for(int i = 0;i<k;i++){
            ms.insert(nums[i]);
        }   
        
        int l = 0;
        for(int i = k-1;i<nums.size();i++){
            auto it = ms.end(); --it; // get max
            ans.push_back(*it);
            ms.erase(ms.find(nums[l])); // delete last left element
            l++;
            if(i+1<nums.size()){ // if possible
                ms.insert(nums[i+1]); // right
            }
        }
        return ans;
    }
};
```
This runs in O(nlogn) time. 
### Monotonic Deque
We can use a queue to do this is O(n) - linear time. Note the queue will not store all elements in the window. Going through the array, we will pop elements from the back of the deque until the back element is smaller than it. This way, since we are including the current element in the window, we dont want any element in the queue that is bigger than it because it is useless cause we already have current element.
Then, we will only delete an element from the front if its the first element in the front of the deque.
In other words, the deque only stores the elements needed to determine a minimum for some subarray(that is, it is nondecreasing). We remove an element only if we've processed that subarray/window(that is, if the left element is the smallest well erase it from the front otherwise do nothing).
This can also be used to implement maximum:
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        deque <int> dq;
        for(int i = 0;i<k;i++){
            while(dq.size() && dq.back() < nums[i]){ // element in dq is smaller than curr
            // so we dont need it
                dq.pop_back();

            }
            dq.push_back(nums[i]);
        }
        vector <int> ans; ans.push_back(dq.front());
        for(int i = k;i<nums.size();i++){
            while(dq.size() && dq.back() < nums[i]){ // element in dq is smaller than curr
            // so we dont need it
                dq.pop_back();

            }
            dq.push_back(nums[i]);
            // delete left element
            if(dq.front() == nums[i-k]){ // if left element is in dq        
                dq.pop_front();
            }
            ans.push_back(dq.front());
        }
        return ans;
    }
};
```


### Two Stacks Method Pt2
We can also use two stacks to calculate max/min


**Example 2:** https://cses.fi/problemset/task/1141

This is a dynamic sliding window problem( just two pointers), meaning the window size changes. We fix the left bound. Then we get the maximum possible right bound. To do this, we maintain a map of elements in the window. 
The left pointer transition is losing the leftmost value. Since all values in the window are distinct, we only need to say $map[value]=0$ meaning that the window no longer contains this value.
The right pointer transition is adding a new value. We check if its possible to add this value using the map and if it is we set $map[value]=1$ meaning the value is in the window. We add as much as possible until we reach the first value that we cant add cause its already in the window.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    int ans = 0;
    int r = 0;
    map <int,int> mp; // is value in current window
    rep(i,0,n){
        while(r < n && mp[v[r]]==0){
            mp[v[r]]=1;
            r++;
        }
        ans = max(ans, r-i); // length, since r is pointing to the value we cant add
        mp[v[i]]=0; // set back to 0 since we're moving window
    }
    cout<<ans<<endl;
}   

```
