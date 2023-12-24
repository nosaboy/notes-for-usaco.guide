## Usage
- Update single element of array in O(logn)
- Query sum/any **associative** operation of range $[l...r]$ in O(logn)

### Segment Trees
Do point update and range query in O(logn) for any **associative operation:** ((a#b)#c = a#(b#c))
- sum/product
- max/min
- gcd/lcm
Segtree had O(n) preprocessing and O(logn) query. Note that this time complexity must be multiplied by how long it takes to calculate our associative function a#b. So gcd will take O(nlogn) preprocessing and O((logn)^2) query.
#### ITMO CF Edu
Implement 2 operations:
- set(i,v) // set arr[i] = v
- sum(l,r) // query sum [l...r): include left bound, dont include right bound(makes impl simpler)
  
Example:  
First construct binary tree:
- Each node is sum of two elements below it
- Each element in array is a leaf in the tree( we can fill the binary tree with extra 0s, so the # of leafs of the tree will be at most 2n)

First operation set(i,v):
We must update every node in the binary tree that is affected by this change, so every ancestor of leaf i, we must add the difference of v-a_i to it. There are logn nodes(one node per level) above it so this operation takes O(logn) time.

Second operation sum(l,r):
We start from the root and dfs down. 
For every node containing some segment $[x...y]$, we check if this segment is completely inside or outside the desired segment $[l...r]$. 
If its completely inside, we add this to answer, then stop dfs downwards since any node below it will be included in this sum. 
If the segment is completely outside, we stop dfs and dont add anything since any node below it is also not in the segment. 
If a part of current segment x...y is inside l...r and the other part is outside, we must keep dfs since there is some sum below current node we must add, but we dont add current segment x...y since some is outside of desired segment l...r, so we keep looking down until we reach a node that is entirely in desired segment l...r.
**Recap dfs():** 
- If x...y not in l...r: break
- If x...y completely in l...r: ans += sum_node, break
- If x...y partly in l...r: dfs(leftnode),dfs(right node)
**Time Complexity:**
We note that we only dfs further only if x...y contains the border l or border r. Thats the only way that x...y is partially inside l...r. Otherwise, we will just calculate and break(not dfs any further).
For each level, all nodes have distinct segments that do not cross. Thus, at most one segment contains left border and one segment contains right border, so we dfs further at most 2 times. This will run in ~O(4logn) = O(logn) time.

**Implementation:**
We number nodes left to right level by level starting from root = node 0. This way, the left child of n is 2n+1 and the right child is 2n+2.
We calculate segment on the fly: If we are at node n with left border l and right border r-1:$[l...r)$,
- the segment of left node 2n+1 is $[l...m)$
- the segment of right node 2n+2 is $[m...r)$
where m = mid = (l+r)/2. 
**Example 1:** https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/A
Query and Update SUM of ranges using segtree implementation
```cpp
// create segtree struct
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
    void build(vector <int> &a){
        build(a,0,0,sz);
    }
    void build(vector <int> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
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
        sum[x] = sum[2*x+1] + sum[2*x+2];
    }
    void set(int i, int u){
        set(i,u,0,0,sz);
    }
    void set(int i, int u, int x, int lx, int rx){ // assign ith element to u and update sums above it
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
        sum[x] = sum[2*x+1] + sum[2*x+2];
    }
    ll dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    ll dfs(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return 0;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        ll osa = dfs(l,r,2*x+1,lx,m);
        ll nosa = dfs(l,r,2*x+2,m,rx);
        return osa + nosa;
    }
 
};
 
void solve(){
    int n,q;cin>>n>>q;
    vi v;
    segtree st; // create segtree
    st.init(n); // initialize segtree to size n
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        // st.set(i,u,0,0,n); // builds segtree by putting value u at leaf i and calculating sum of nodes
        // start at root node(0), left = 0 right = n is range of whole array
    }
    st.build(v); // Optimization: build segtree in linear time 
    while(q--){
        int o;cin>>o;
        if(o==1){
            int i,u;cin>>i>>u;
            st.set(i,u);
        }
        else{ // o == 2
            int l,r;cin>>l>>r;
            cout<<st.dfs(l,r)<<"\n";
        }
    }
}
    
```

**Example 2:** https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/B
Query and Update MINIMUM value.
Its pretty much the same thing as finding sum, but we replace addition with min().

```cpp
// create segtree struct
struct segtree{
    int sz;
    vector <ll> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,1000000000000000000LL); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <int> &a){
        build(a,0,0,sz);
    }
    void build(vector <int> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
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
        sum[x] = min(sum[2*x+1] , sum[2*x+2]);
    }
    void set(int i, int u){
        set(i,u,0,0,sz);
    }
    void set(int i, int u, int x, int lx, int rx){ // assign ith element to u and update sums above it
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
        sum[x] = min(sum[2*x+1] , sum[2*x+2]);
    }
    ll dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    ll dfs(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return 1000000000000000000LL;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        ll osa = dfs(l,r,2*x+1,lx,m);
        ll nosa = dfs(l,r,2*x+2,m,rx);
        return min(osa, nosa);
    }

};

void solve(){
    int n,q;cin>>n>>q;
    vi v;
    segtree st; // create segtree
    st.init(n); // initialize segtree to size n
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        // st.set(i,u,0,0,n); // builds segtree by putting value u at leaf i and calculating sum of nodes
        // start at root node(0), left = 0 right = n is range of whole array
    }
    st.build(v); // Optimization: build segtree in linear time 
    while(q--){
        int o;cin>>o;
        if(o==1){
            int i,u;cin>>i>>u;
            st.set(i,u);
        }
        else{ // o == 2
            int l,r;cin>>l>>r;
            cout<<st.dfs(l,r)<<"\n";
        }
    }
}
   
```


**Example 3:** https://codeforces.com/edu/course/2/lesson/4/1/practice/contest/273169/problem/C
Calculate NUMBER OF OCCURANCE of minimum value of segment.
This is also associative since we can just pick the smaller element.

```cpp
struct segtree{
    int sz;
    vector <pi> val; // value, num of occurance
    pi merge(pi osa, pi nosa){ // compare two pairs: return min, num of occurance of min
        // we take the occurance of smaller value
        // if they are same, num of occurance = sum of their occurances
        if(osa.first < nosa.first){ 
            return osa;
        }
        if(osa.first > nosa.first){
            return nosa;
        }
        return {osa.first, osa.second + nosa.second};
    }
    void init(int n){ 
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.resize(2*sz); // make vector contain exactly 2*sz elements
    }
    void build(vector <int> &a){
        build(a,0,0,sz);
    }
    void build(vector <int> &a, int x, int lx, int rx){
        if(rx - lx == 1){ // base case(we arrived at leaf)
            if(lx < (int)a.size()){
                val[x] = {a[lx],1}; // there is 1 occurance with the min value(the current number)
            }
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        val[x] = merge(val[2*x+1], val[2*x+2]); // calc val
       
    }
    void set(int i, int u){
        set(i,u,0,0,sz);
    }
    void set(int i, int u, int x, int lx, int rx){ 
        if(rx-lx==1){ 
            val[x] = {u,1}; // 1 occurance with min value
            return;
        }
        int m = (lx+rx)/2; 
        if(i < m){
            set(i, u, 2*x+1,lx,m);
        }
        else{ 
            set(i, u, 2*x+2,m,rx);
        }
        val[x] = merge(val[2*x+1], val[2*x+2]); // calc val
    }
    pi dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    pi dfs(int l, int r, int x, int lx, int rx){ 
        if(r <= lx || rx <= l){ 
            return {1000000005,0}; // neutral value since we dont want this to be included in ans
        }
        if(l <= lx && rx <= r){ 
            return val[x];
        }
        int m = (lx+rx)/2;
        pi osa = dfs(l,r,2*x+1,lx,m);
        pi nosa = dfs(l,r,2*x+2,m,rx);
        return merge(osa , nosa); // calc ans
    }

};

void solve(){
    int n,q;cin>>n>>q;
    vi v;
    segtree st; 
    st.init(n); 
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    st.build(v); 
    while(q--){
        int o;cin>>o;
        if(o==1){
            int i,u;cin>>i>>u;
            st.set(i,u);
        }
        else{ // o == 2
            int l,r;cin>>l>>r;
            pi ans = st.dfs(l,r);
            cout<<ans.first<<" "<<ans.second<<"\n";
        }
    }
}

```
