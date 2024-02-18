### CF EDU: https://codeforces.com/edu/course/2/lesson/4/2
**Problem 1:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/A
Define two operations:
- Update position $i$ to $u$
- Find the maximum subarray sum of the whole array.

For each segment, we can calculate subsegment with maximal sum.
TO do this, we want to calculate for each segment
- The maximum subarray sum in segment
- The maximum prefix sum
- The maximum suffix sum
- The sum of the whole array
Then, the mergining will be either:
the maximum segment sum of left, the maximum segment sum of right, or a subarray intersecting left and right.
The first two case we an just use max subarray sum value. For the third value, it is $suf_{left} + pref_{right}$.
The new pref and suf can either be $prev_{left}$ and $suf_{right}$, but we will also consider if new pref will intersect the two left and right. Thus we have to calc sum of whole array to deal with this case. 
We then recalculate pref,suf,mx as follows:
- mx = $max(mx_{left}, mx_{right}, suf_{left} + pref_{right})$
- pref = $max(pref_{left}, sum_{left}+pref_{right})$
- suf = $max(suf_{right},suf_{left}+sum_{right})$
- sum = $sum_{left}+sum_{right}$


**To calculate maximum subarray sum of a fixed segment (l,r), we essentially create a function that does the same as normal segtree range queries, but merge with these operations.**
```cpp
// MAX SUBARRAY SUM SEGTREE GIVEN INTERVAL
struct item{
 
    ll seg, pref, suf, sum;
};
 
struct segtree{
    int sz;
    vector <item> val; // store sum of nodes
 
    // CAN BE CHANGED
 
    item neutral = {0,0,0,0}; // EMPTY SEGMENT: we can add this element 
    // to anything and get the original result 
 
    item merge(item a, item b){ // merge two nodes( their items)
        return { // use formula to merge two items
            max(max(a.seg, b.seg), a.suf + b.pref), // mx
            max(a.pref, a.sum+b.pref), // pref
            max(b.suf,a.suf+b.sum), // suf
            a.sum + b.sum // sum
        };
    }
 
    item single(ll v){ // we are left with single node( base case)
        // v is our current single element( length 1)
        if(v>0){ // v is positive, so max seg, pref, suf, sum is v
            return {v,v,v,v}; // since its optimal to just pick this element
        }
        else{ // if element is negative, it is optimal to just pick empty 
            return {0,0,0,v}; // we dont choose v as our answer for first 3, but sum is v
        }
 
    }
 
    // CODE FOR IMPL
 
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,neutral); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <ll> &a){
        build(a,0,0,sz);
    }
    void build(vector <ll> &a, ll x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                val[x] = single(a[lx]);
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            val[x] = single(u);
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
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    item query(int l, int r){
        return query(l,r,0,0,sz);
    }
    item query(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return neutral;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return val[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        item osa = query(l,r,2*x+1,lx,m);
        item nosa = query(l,r,2*x+2,m,rx);
        return merge(osa, nosa);
    }
 
};
 
 
void solve(){
    int n,q;cin>>n>>q;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    segtree st;
    st.init(n);
    st.build(v);
    cout<<st.query(0,n).seg<<endl; // calc whole array
    while(q--){
        int i,v;cin>>i>>v;
        st.set(i,v);
        cout<<st.query(0,n).seg<<endl;
    }
}   

```
**It is also possible to get the EXACT segment itself( output: l,r that produces max), we must add more values for borders and stuff**


**Problem 2:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/B
Define two operations:
- Update position $i$ to $u$, $u$ is 0 or 1
- Find the position of the kth 1 value

tbh we can just use ordered multisets.

For the segtree way, we maintain a sum of 1s in range like normal. Then, if the left node is >= target, we go left. Else, we go right since it must then be >= target. We have now reduced this problem to only the subtree. We then subtract our target to see how many things we skipped(if we picked left, we subtract leftsum from target). We then want to find the target-th 11 in this subtree. So logic is
- If left node( sum) >= target pos, visit left and target is the same
- Else If right node( sum) >= target pos, visit right and target -= left sum

```cpp
// kth element
typedef ll item;
 
struct segtree{
    int sz;
    vector <item> val; // store sum of nodes
 
    // CAN BE CHANGED
 
    item neutral = 0; // EMPTY SEGMENT: we can add this element 
    // to anything and get the original result 
 
    item merge(item a, item b){ // merge two nodes( their items)
        return  a + b; // use formula to merge two items
            
        
    }
 
    item single(ll v){ // we are left with single node( base case)
        // v is our current single element( length 1)
        return v;
 
    }
 
    // CODE FOR IMPL
 
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,neutral); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <ll> &a){
        build(a,0,0,sz);
    }
    void build(vector <ll> &a, ll x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                val[x] = single(a[lx]);
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            val[x] = single(u);
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
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    item query(int l, int r){
        return query(l,r,0,0,sz);
    }
    item query(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return neutral;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return val[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        item osa = query(l,r,2*x+1,lx,m);
        item nosa = query(l,r,2*x+2,m,rx);
        return merge(osa, nosa);
    }
    item find(int k, int x, int lx, int rx){
        if(rx-lx==1){ // current node is a leaf
            return lx;
        }
        int m = (lx+rx)/2; // mid
        int osa = val[2*x+1]; // left subtree
        if(osa > k){ // left >= target sum
            // since 1s are numbered from 0, we do osa>k
            return find(k, 2*x+1,lx,m);
        }
        else{
            return find(k-osa, 2*x+2,m,rx);
        }
    }
    item find(int k){
        return find(k,0,0,sz);
    }
};
 
 
void solve(){
    int n,q;cin>>n>>q;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    segtree st;
    st.init(n);
    st.build(v);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int i;cin>>i;
            v[i] = 1-v[i];
            st.set(i,v[i]);
        }
        else{
            int k;cin>>k;
            cout<<st.find(k)<<endl;
        }
    }
}   
 
```
**Problem 3:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/C
Define two operations:
- Updateposition $i$ to $u$ for given $i$ and $u$
- Find the minimum position $i$ such that $a[i] >= x$ for given $x$

Here we are keeping the ordering of the array and ans is based on position, so we cant use ordered multisets. We can create a max segtree. Then, it follows similar logic as above:
- If left node( max) >= target, visit left
- Else If right node( max) >= target pos, visit right

since if left < target, right must have value >= left

```cpp
typedef ll item;
 
struct segtree{
    int sz;
    vector <item> val; // store sum of nodes
 
    // CAN BE CHANGED    
 
    // MAX SEGTREE 
 
    item neutral = -1; // EMPTY SEGMENT: we can add this element 
    // to anything and get the original result 
 
    item merge(item a, item b){ // merge two nodes( their items)
        return  max(a, b); // use formula to merge two items
            
        
    }
 
    item single(ll v){ // we are left with single node( base case)
        // v is our current single element( length 1)
        return v;
 
    }
 
    // CODE FOR IMPL
 
    void init(int n){ // create empty segtree with length at lea---st n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,neutral); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <ll> &a){
        build(a,0,0,sz);
    }
    void build(vector <ll> &a, ll x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                val[x] = single(a[lx]);
            }
            // else we just fill with 0 so dont do anything
            return;
        }       
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            val[x] = single(u);
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
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    item query(int l, int r){
        return query(l,r,0,0,sz);
    }
    item query(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return neutral;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return val[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        item osa = query(l,r,2*x+1,lx,m);
        item nosa = query(l,r,2*x+2,m,rx);
        return merge(osa, nosa);
    }
    item find(int k, int x, int lx, int rx){
        if(rx-lx==1){ // current node is a leaf 
            if(val[x] >= k){ // if its valid
                return lx;
            }
            else{ // pos is invalid
                return - 1;
            }
        }
        int m = (lx+rx)/2; // mid
        ll osa = val[2*x+1]; // left subtree
        ll nosa = val[2*x+2]; // right 
        if(osa >= k){ // left >= target, so we know its in left
            // since 1s are numbered from 0, we do osa>k
            return find(k, 2*x+1,lx,m);
        }
        else if(nosa >= k){ // else we must pick less optimal
            return find(k, 2*x+2,m,rx);
        }
        else{ // entire segment is invalid
            return -1;
        }
    }
    item find(int k){
        return find(k,0,0,sz);
    }
};
 
 
void solve(){
    int n,q;cin>>n>>q;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    segtree st;
    st.init(n);
    st.build(v);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int i,v;cin>>i>>v;
            st.set(i,v);
        }
        else{
            int k;cin>>k;
            cout<<st.find(k)<<endl;
        }
    }
}   
```
**Problem 4:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/D
Define two operations:
- Updateposition $i$ to $u$ for given $i$ and $u$
- Find the minimum position $i$ such that $a[i] >= x$ AND $i >= l$ for given $x$ and $l$

This is problem 3 but we  have restrictions.
We will still visit a path if its maximum is >= u. However, whenever we find that our segment is not in range [l...n), we will break immediately. However, we are unsure whether a segment will produce a valid answer because the max element might be outside the range. Unexpectedly, this runs on time because if the left encompasses the range, then the right is in range. Thus, we will only have to visit the left range at most logn times.

```cpp
typedef ll item;
 
struct segtree{
    int sz;
    vector <item> val; // store sum of nodes
 
    // CAN BE CHANGED    
 
    // MAX SEGTREE 
 
    item neutral = -1; // EMPTY SEGMENT: we can add this element 
    // to anything and get the original result 
 
    item merge(item a, item b){ // merge two nodes( their items)
        return  max(a, b); // use formula to merge two items
            
        
    }
 
    item single(ll v){ // we are left with single node( base case)
        // v is our current single element( length 1)
        return v;
 
    }
 
    // CODE FOR IMPL
 
    void init(int n){ // create empty segtree with length at lea---st n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,neutral); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <ll> &a){
        build(a,0,0,sz);
    }
    void build(vector <ll> &a, ll x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                val[x] = single(a[lx]);
            }
            // else we just fill with 0 so dont do anything
            return;
        }       
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            val[x] = single(u);
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
        val[x] = merge(val[2*x+1], val[2*x+2]);
    }
    item query(int l, int r){
        return query(l,r,0,0,sz);
    }
    item query(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return neutral;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return val[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        item osa = query(l,r,2*x+1,lx,m);
        item nosa = query(l,r,2*x+2,m,rx);
        return merge(osa, nosa);
    }
    item find(int k, int l, int x, int lx, int rx){
        if(rx-lx==1){ // current node is a leaf 
            if(val[x] >= k){
                return lx;
            }
            else{
                return - 1;
            }
        } 
        int m = (lx+rx)/2; // mid
        ll osa = val[2*x+1]; // left subtree
        ll nosa = val[2*x+2]; // right 
        int ans = -1;
        if(osa >= k && m > l){ // segment is contained in [l,n)
            // since 1s are numbered from 0, we do osa>k
            ans = find(k, l, 2*x+1,lx,m);
 
        }
        if(ans==-1){ // didnt find in left segment
            if(nosa >= k && rx > l){ // segment is contained in [l,n)
                return find(k, l, 2*x+2,m,rx);
            }
            return -1; // didnt find in segement
        }
        else{
            return ans;
        }
        
    }
    item find(int k, int l){
        return find(k,l,0,0,sz);
    }
};
 
 
void solve(){
    int n,q;cin>>n>>q;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    segtree st;
    st.init(n);
    st.build(v);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int i,v;cin>>i>>v;
            st.set(i,v);
        }
        else{
            int k,l;cin>>k>>l;
            cout<<st.find(k,l)<<endl;
        }
    }
}   
 
 
```
### Maximum Subarray Sum Queries
**Example:** https://cses.fi/problemset/task/1190


**Problem of Interest:** https://codeforces.com/problemset/problem/1906/F
We look at every $k$. For every query, we calculate for that point the maximum subarray 
