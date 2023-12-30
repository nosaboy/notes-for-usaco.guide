## Usage
- Query on subtrees
  - Just Segtree but on trees
 
- Query LCA 

### How it works
We visit the tree starting form the root using dfs. We then store the **order** of nodes we've visited in a 1D array. Thus, any subtree will be a continous segment on the array since we are visiting all children of a node before dfsing back. 
The idea is to preprocess a rooted tree such that every subtree corresponds to a contiguous range on an array. We are pretty much forcing subtrees to be together so we can perform **range query updates** on subtrees.
#### Subtree Queries
We can thus **query associative operations on subtrees** using a segtree data structure, since we now have the 1D array.
We must first store the order for every node, then the size of that node. This is because we note that after visiting this node, every node below it will be visited next until we dfs back. Thus, the range of subtree x on the ordering array is just position_x to position_{x+size of subtree x}. 
**Example 1:** https://cses.fi/problemset/task/1137
Update and query subtree sums
**Implementation:**
We want to maintain 3 arrays: 
- Ordering of each node(what is its position on the 1d array/segtree)
- Subtree size(for interval length)
- Value of each node(value in the segtree for each node/index)

To get ordering, we just dfs through the tree normally and keep the ordering vector. For each unvisited node, we push it into the vector, then visit its children.
To get subtree size, we just do dp.
To update value, we get position of node in the segtree($start[i]$), then update the segtree.
To get sum of interval, we first get position of node $i$ and size of nodes subtree $x$, then query segment tree for $sum[i...i+x).$
Thus, we can update and query subtrees in O(logn).
```cpp
// SEGTREE TO QUERY SUM 
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
        return osa + nosa;
    }
 
};
 
vi order; int start[200005]; int sz[200005]; ll val[200005]; // value of node
vi aj[200005]; int vis[200005];
void dfs(int n){
    vis[n] = true;
    order.pb(n); // next node in ordering
    start[n] = (int)order.size()-1; // 0 indexed
    sz[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
            sz[n] += sz[aj[n][i]];
        }
    }
}
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(n+1);
    vi v;
    rep(i,0,n){
        cin>>val[i];
    }
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        a--;b--;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(0);
    vector <ll> a; // value of each node in its position in ordering array
    rep(i,0,n){
        a.pb(val[order[i]]); 
    }
    st.build(a);
    while(q--){
        int c;cin>>c;
        if(c==1){
            int i,u;cin>>i>>u; 
            i--;
            st.set(start[i],u); // start[i] = position of node i in segtree
        }
        else{
            int i;cin>>i; 
            i--;
            cout<<st.query(start[i], start[i]+sz[i])<<endl; 
        }
    }
    
}
```
**Alternative implementation:** to calculate range of a node i, we can also use a timer. We first set $start[i]$ as node i's position in the array. After we visited all its neighbours and we come back from our dfs, we set $end[i]$ as the time at the end. This way, if we want to query range it will just be $st.query(start[i], end[i])$. We also note that $end[i]-start[i] = subtreesize[i].$

#### Path Queries
**Problem 2:** https://cses.fi/problemset/task/1138
We want to calculate assoiative operations on paths instead of subtrees.
We can perform update node value and calculate path sum both in O(logn)
To update the value of a node x, we are affecting the answer of all nodes in the subtree of x as well, since going from any node in the subtree to 1 must pass curr node x. Thus, we can perform range update on all subtree of x. 
Lets again use eulers tour to make a 1D array of the values of subtrees. Then, we can do this similar to Example 8 in Point Update Range Sum. 
This is because we can maintain for any node x that its value is only added to its subtree. Since the subtree is a continous subarray on the segtree, we just add the difference to start_x and subtract diff to end_x(start + sz) since we are not going to add this to any other node other than its subtree.
Then, for every query of path we just calculate the sum 0...start_x just like Example 8.

```cpp
// SEGTREE TO QUERY SUM 
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
        sum[x] = sum[2*x+1] + sum[2*x+2];
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
        sum[x] = sum[2*x+1] + sum[2*x+2];
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
        return osa + nosa;
    }
 
};
 
vi order; int start[200005]; int sz[200005]; ll val[200005]; // value of node
vi aj[200005]; int vis[200005];
void dfs(int n){
    vis[n] = true;
    order.pb(n); // next node in ordering
    start[n] = (int)order.size()-1; // 0 indexed
    sz[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
            sz[n] += sz[aj[n][i]];
            
        }
    }
}
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(n+1);
    vector <ll> v;
    rep(i,0,n){
        cin>>val[i];
    }
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        a--;b--;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(0);
    vector <ll> a(n); // value of each node in its position in ordering array
    rep(i,0,n){ // precalculate each step so we can calculate path sum
        a[i] += val[order[i]]; 
        a[i+sz[order[i]]]-= val[order[i]]; 
    }
    st.build(a);
    while(q--){
        int c;cin>>c;
        // range update
        // code from range update and calculate single value: Example 8 in point update range sum
        if(c==1){
            ll i,u;cin>>i>>u;
            i--; // 1 indexed

            ll left = st.query(start[i],start[i]+1); // get left value node
            ll right = st.query(start[i] + sz[i],start[i] + sz[i]+1); // get right value node

            st.set(start[i],left+u-val[i]); 
            st.set(start[i] + sz[i],right-(u-val[i])); 
            val[i] = u;
        }
        else{ // c == 2
            int i;cin>>i;i--;
            cout<<st.query(0,start[i]+1)<<endl; // prefix sum of position i = curr value
        }
    }
    
}
```


## Lowest Common Ancestor
Given two nodes a and b, find the lowest node(furthest away from root) such that its subtree contains both a and b. 
Efficient query LCA of two nodes using Eulers Tour Technique:
We traverse through tree using Eulers Tour. However, everytime we revisit a node while walking(doesnt have to be just the first time), we add it to order vector. Thus, a node with k children will be visited k+1 times(and thus there are k+1 instances in the array). In total, the array size is 2n-1. In the array, we will also store its depth from root. Thus, the array is
```cpp
vector <pi> order; // pair = {id index of node, depth of node}
```
For each query to find LCA of node a and b, the LCA is the node that has the **minimum depth in segment between a and b** on the order array. 
This is because after going to a, we traverse back to the LCA, then go down to b. We will not visit any higher nodes than the LCA during this time.
We can query this using RMQ data structure such as segtree or sparse table since the **array is static**.
Note: In this case that there are multiple a and b in the array, we can just take any index of a and b. This is because all depths after the first visit of node a will be lower than node a until the last visit of node a. Similar for node b due to order which we traverse the tree. Thus, we dont have to worry about that. Moreover, LCA must be unqiue, so we can just find the smallest without worrying about anything else.
Therefore, we can just store the first position of node i in order in $start[i.]$ So the LCA is just $RMQ(start[a],start[b])$ where RMQ compares its depths.

**Example 2:** https://cses.fi/problemset/task/1688
LCA queries implementation:
```cpp
// MIN SEGTREE ON DEPTH(pair)
struct segtree{
    int sz;
    vector <pi> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,{0,0}); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <pi> &a){ // {depth, node id}
        build(a,0,0,sz);
    }
    void build(vector <pi> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                sum[x] = {a[lx].first,a[lx].second};
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        if(sum[2*x+1].first < sum[2*x+2].first){
            sum[x] = sum[2*x+1];
        }
        else{
            sum[x] = sum[2*x+2];
        }
        
    }
    // static, doesnt need set()
    pi query(int l, int r){
        return query(l,r,0,0,sz);
    }
    pi query(int l, int r, int x, int lx, int rx){ // min of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return {1000000000,1000000000}; // base
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        pi osa = query(l,r,2*x+1,lx,m);
        pi nosa = query(l,r,2*x+2,m,rx);
        // return whichever node is smaller
        if(osa.first < nosa.first){ 
            return osa;
        }
        else{
            return nosa;
        }
    
    }
 
};

// EULERS TOUR TO FIND ORDER
vi order; int start[200005]; int sz[200005]; int depth[200005];
vi aj[200005]; int vis[200005];
void dfs(int n){
    vis[n] = true;
    order.pb(n); // next node in ordering
    start[n] = (int)order.size()-1; // 0 indexed
    sz[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            depth[aj[n][i]]=depth[n]+1; // dp calc depth of node
            dfs(aj[n][i]);
            order.pb(n); // came back
            sz[n] += sz[aj[n][i]];
            
        }
    }
}
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(2*n-1); // there is at most 2n-1 nodes in order arr

    rep(i,2,n+1){
        int u;cin>>u;
        aj[u].pb(i);
        aj[i].pb(u);
    }
    depth[1]=0;
    dfs(1);
    vector <pi> a;
    rep(i,0,order.size()){
        a.pb({depth[order[i]],order[i]});
    }
    st.build(a);
    while(q--){
        int x,y;
        cin>>x>>y;
        // make sure they are in correct order
        int l,r;
        if(start[x] < start[y]){
            l = start[x];
            r = start[y];
        }
        else{
            r = start[x];
            l = start[y];
        }
        pi ans = st.query(l,r+1); // find LCA
        cout<<ans.second<<endl;
    }
}
```
**Problem of Interest:** https://codeforces.com/problemset/problem/1702/G2
We can see if all nodes satisfy some lca property. First, we see if every node is on a straight line to the top without going up, then going down. If it is, we just print YES. Else, we know that we must go up to some lca node, then go down. To get lca, we find the two nodes such that they have the lowest depth, and they have different lca. These two nodes x and y will be out ending points. Meaning the path is going from x to lca(x,y), then going back down to y.
Now we just need to check if all nodes is on this path. To do this, we note that for each node, that node and lca(x,y) must = lca(x,y). Else, its lca has smaller depth than lca(x,y), which means it is not on the path. Moreover, it must either be on the path from x to lca(x,y) or on the path from y to lca(x,y). Thus, we check if lca(v_i,x) = lca(x,y), which means it is on path x -> lca(x,y), or check if lca(v_i,y) = lca(x,y), which means it is on path y -> lca(x,y). 
If all these conditions do not satisfy, this node is not on the path so we print NO and break.
At the end if every node satisfies these conditions we print YES.

```cpp
// MIN SEGTREE ON DEPTH(pair)
struct segtree{
    int sz;
    vector <pi> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,{0,0}); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <pi> &a){ // {depth, node id}
        build(a,0,0,sz);
    }
    void build(vector <pi> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                sum[x] = {a[lx].first,a[lx].second};
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        if(sum[2*x+1].first < sum[2*x+2].first){
            sum[x] = sum[2*x+1];
        }
        else{
            sum[x] = sum[2*x+2];
        }
        
    }
    // static, doesnt need set()
    pi query(int l, int r){
        return query(l,r,0,0,sz);
    }
    pi query(int l, int r, int x, int lx, int rx){ // min of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return {1000000000,1000000000}; // base
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        pi osa = query(l,r,2*x+1,lx,m);
        pi nosa = query(l,r,2*x+2,m,rx);
        // return whichever node is smaller
        if(osa.first < nosa.first){ 
            return osa;
        }
        else{
            return nosa;
        }
    
    }
 
};
// EULERS TOUR TO FIND ORDER
vi order; int start[200005]; int sz[200005]; int depth[200005];
vi aj[200005]; int vis[200005];
void dfs(int n){
    vis[n] = true;
    order.pb(n); // next node in ordering
    start[n] = (int)order.size()-1; // 0 indexed
    sz[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            depth[aj[n][i]]=depth[n]+1; // dp calc depth of node
            dfs(aj[n][i]);
            order.pb(n); // came back
            sz[n] += sz[aj[n][i]];
            
        }
    }
}
 
void solve(){
    int n,q;cin>>n;
    segtree st;
    st.init(2*n-1); // there is at most 2n-1 nodes in order arr
 
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    depth[1]=0;
    dfs(1);
    vector <pi> a;
    rep(i,0,order.size()){
        a.pb({depth[order[i]],order[i]});
    }
    st.build(a);
    cin>>q;
    while(q--){
        int m; cin>>m;
        vector <pi> v;
        rep(i,0,m){
            int u;cin>>u;v.pb({depth[u],u});
        }
        sort(v.rbegin(),v.rend());
        int x = v[0].second;
        int y;
        bool yn = true;
        int osa = 0; // best lca
        rep(i,1,m){
            // make sure they are in correct order
            int l,r;
            y = v[i].second;
            if(start[x] < start[y]){
                l = start[x];
                r = start[y];
            }
            else{
                r = start[x];
                l = start[y];
            }
            pi ans = st.query(l,r+1);
            int lca = ans.second; // lca node 
            if(lca != x && lca != y){
                osa = lca;
                yn = false;
                break;
            }
        }
        if(yn){ // same line
            cout<<"YES\n";
        }
        else{
            //cout<<x<<" "<<y<<endl;
            rep(i,0,m){
                // lca(x,v[i])
                int l,r;
                if(start[x] < start[v[i].second]){
                    l = start[x];
                    r = start[v[i].second];
                }
                else{
                    r = start[x];
                    l = start[v[i].second];
                }
                pi ans = st.query(l,r+1);
                int lca1 = ans.second; 
                // lca(y,v[i])
                if(start[y] < start[v[i].second]){
                    l = start[y];
                    r = start[v[i].second];
                }
                else{
                    r = start[y];
                    l = start[v[i].second];
                }
                ans = st.query(l,r+1);
                int lca2 = ans.second; 
                // lca(osa,v[i])
                if(start[osa] < start[v[i].second]){
                    l = start[osa];
                    r = start[v[i].second];
                }
                else{
                    r = start[osa];
                    l = start[v[i].second];
                }
                ans = st.query(l,r+1);
                int lca3 = ans.second; 
                //cout<<v[i].second<<" "<<lca1<<" "<<lca2<<" "<<lca3<<endl;
                if(depth[lca3] < depth[osa] || (lca1 != v[i].second && lca2 != v[i].second)){
                    yn = true;
                    cout<<"NO\n";
                    break;
                }
 
            }
            if(!yn){
                cout<<"YES\n";
            }
            
        }
    }
}
```
### Distance of Two Nodes in Tree
Distance of a and b = length of path from a to b in a tree.
We can calculate using LCA, since path of a -> b = a -> LCA(a,b) -> b.
Thus, the distance formula for a and b is dist(a,LCA(a,b)) + dist(LCA(a,b),b) = depth(a) + depth(b) - 2*depth(LCA(a,b)). Depth(x) = dist(x,root). Depth can be calculated using dp.

**Example 3:** https://cses.fi/problemset/task/1135

```cpp
// MIN SEGTREE ON DEPTH(pair)
struct segtree{
    int sz;
    vector <pi> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,{0,0}); // create empty segtree size 2*sz and fill with 0s
    }
    void build(vector <pi> &a){ // {depth, node id}
        build(a,0,0,sz);
    }
    void build(vector <pi> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                sum[x] = {a[lx].first,a[lx].second};
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        if(sum[2*x+1].first < sum[2*x+2].first){
            sum[x] = sum[2*x+1];
        }
        else{
            sum[x] = sum[2*x+2];
        }
        
    }
    // static, doesnt need set()
    pi query(int l, int r){
        return query(l,r,0,0,sz);
    }
    pi query(int l, int r, int x, int lx, int rx){ // min of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return {1000000000,1000000000}; // base
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        pi osa = query(l,r,2*x+1,lx,m);
        pi nosa = query(l,r,2*x+2,m,rx);
        // return whichever node is smaller
        if(osa.first < nosa.first){ 
            return osa;
        }
        else{
            return nosa;
        }
    
    }
 
};
// EULERS TOUR TO FIND ORDER
vi order; int start[200005]; int sz[200005]; int depth[200005];
vi aj[200005]; int vis[200005];
void dfs(int n){
    vis[n] = true;
    order.pb(n); // next node in ordering
    start[n] = (int)order.size()-1; // 0 indexed
    sz[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            depth[aj[n][i]]=depth[n]+1; // dp calc depth of node
            dfs(aj[n][i]);
            order.pb(n); // came back
            sz[n] += sz[aj[n][i]];
            
        }
    }
}
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(2*n-1); // there is at most 2n-1 nodes in order arr

    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    depth[1]=0;
    dfs(1);
    vector <pi> a;
    rep(i,0,order.size()){
        a.pb({depth[order[i]],order[i]});
    }
    st.build(a);
    while(q--){
        int x,y;
        cin>>x>>y;
        // make sure they are in correct order
        int l,r;
        if(start[x] < start[y]){
            l = start[x];
            r = start[y];
        }
        else{
            r = start[x];
            l = start[y];
        }
        pi ans = st.query(l,r+1); // find LCA
        // THE CODE BELOW IS EVERYTHING ADDED FROM EXAMPLE 2
        int lca = ans.second; // lca node 

        cout<<depth[x] + depth[y] - 2*depth[lca]<<endl; // dist formula
    }
}
```

## Sparse Table
- RMQ On static(not changing) arrays: O(nlogn) preprocessing, **O(1) query**

Implementation:
Precompute minimum for all range $[a,b]$ whose length b-a+1 is a power of 2. This can be done in O(nlogn).
```cpp
int lg2(int n){ // calculate floor(log2(n)) in O(1)
    return 31 - __builtin_clz(n);
}
vector<vi> ST(const vi& v) {
  int n = v.size();
  vector<vi> ST(lg2(n)+1,vi (n,INT_MAX)); // ST[log2(n)][n]
  ST[0] = v; // min for length 2^0 = 1 is just the number
  rep(len,1,ST.size()) { // for every power of 2
    rep(i,0,n - (1 << len) + 1) { // for every index starting at i with length 2^len
      ST[len][i] = min(ST[len - 1][i], ST[len - 1][i + 1 << (len - 1)]); 
      // divide range into two since we calculated previous power of 2
      // min(i...i+(2^len-1)-1, i+(2^len-1)...i+(2^len)-1)
    }
  }

  return ST;
}
```
**Example 4:** https://judge.yosupo.jp/problem/staticrmq
