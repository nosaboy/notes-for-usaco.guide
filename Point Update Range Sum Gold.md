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
https://cses.fi/problemset/task/1648/ - We adjust our index since this problem is 1 indexed instead of 0 indexed(like the cf problem)
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
https://cses.fi/problemset/task/1649 - Adjust index since problem query is 1 indexed instead of 0

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

**Example 4:** https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/A
https://www.spoj.com/problems/INVCNT/
Given permutation, calculate number of inversions(all pairs i,j such that i < j and $a[i] > a[j]$)

We can brute force the solution, then optimize with segtree. The brute force solution is O(n^2), where we just check for each i, go through all j smaller than it(i > j) and see and $a[i] < a[j]$ is satisfied.

We can speed check every j using segtrees. We keep track of visited elements for all j before i and mark it as 1 in the segtree. Then, we go through the segtree and only add the elements we've visited that is bigger than the current element. This satisifies $a[j] > a[i]$ and i<j at the same time since we only stored elements before i so $i>j$. This reduce to O(nlogn) since for every i we update and query segtree in logn.

We maintain segtree that counts the number of occurances of each number(leaf_j = number of v_j that came before i, so number of elements bigger than curr is just sum(currv_i...n)).

We can avoid segtree using orderedsets.
```cpp
// SEGTREE TO QUERY SUM 
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
    int n,q;cin>>n;
    vi v;
    segtree st; // create segtree
    st.init(n); // initialize segtree to size n
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        // st.set(i,u,0,0,n); // builds segtree by putting value u at leaf i and calculating sum of nodes
        // start at root node(0), left = 0 right = n is range of whole array
    }
    // the segtree is originally 0 since it represents the number of each value
    rep(i,0,n){
        
        cout<<st.dfs(v[i]-1, n)<<" "; // query sum of all elements from v_i to n.
        st.set(v[i]-1,1); // all values distinct(v is permutation)
        
    }

}
```

**Example 5:** https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/B
Given inversions, find original array.

- Sometimes we approach/build smth by looking at it a different way. In this case, we look at it from right to left.
- We want to FORCE some elements based on given info, so we know that some element HAS TO BE IN THAT POSITION.

Looking at it from right to left, we note that the inversion of the right most element is just its position in the permutation. So looking at the last element, if there is 3 elements before it thats bigger than it, then it must mean that element is 2 since every element in the permutation is before it. Thus, we dont have to guess anything since we know that after we process the last element, the second last element becomes the last. So for each element k we process, we just find the n-kth element in the array and return that since there will be k elements bigger than it.

I was lazy so I just used ordered multiset and did it. 
NOTE: Dont erase in orderedmultiset using ms.erase(ms.find(val)). Instead, use ms.erase(ms.find_by_order(val));

```cpp
void solve(){
    int n,q;cin>>n;
    vi v;
    orderedMultiset <int> ms;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        ms.insert(n-(i+1));
    }
    vi ans;
    for(int i = n-1;i>=0;i--){
        int osa = *ms.find_by_order(v[i]);
        ms.erase(ms.find_by_order(v[i]));
        ans.pb(-(osa-n));
    }
    reverse(ans.begin(),ans.end());
    rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }

}
    

```

**Example 6:** https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/C
Given segments, determine number of pairs of segments such that pair j is completely contained inside pair i.

We note that a pair x is contained in pair y in the following case: y...x...x...y, so if ly <= lx <= rx <= ry. Thus, we can for each segment interval store the count of segments inside it and query using segtree. We can calculate for each y the number of x inside its range. To do this, we first iterate through the first y and store its position as $start[y]=i.$ Then, when we get to the second y(right border of segment), we first calculate the number of segments x inside the range ly...ry using our segtree, then we store this count in our segtree. We note that if some range z encompasses ly...ry, then lz must be before ly and rz must be after ry. We know that after this we will only process elements after ry, so we only need to satisfy for every element z we process that lz < ly. To do this, we can just increase position ly in the segtree by 1. This way, whenever lz < ly, then querying sum of segtree on range lz...rz will add 1 to our sum.

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
    int n,q;cin>>n;
    vi v;
    segtree st; // create segtree
    st.init(2*n); // initialize segtree to size n
    int vis[n+1]; // starting point of segment
    rep(i,0,2*n){
        int u;cin>>u;v.pb(u);
        vis[u]=-1;
        
    }
    // the segtree is originally 0 since it represents the number of each value
    int ans[n+1];
    rep(i,0,2*n){
        if(vis[v[i]] >= 0){ // process segment
            ans[v[i]] = st.dfs(vis[v[i]], i); // query sum of all elements from v_i to
            st.set(vis[v[i]],1); // all values distinct(v is permutation)
        }
        else{
            vis[v[i]]=i;
        }
    }
    rep(i,1,n+1){
        cout<<ans[i]<<" ";
    }
 
 
}

```

**Example 7:** https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/D
We note that there are two cases where 2 segments intersect: x...y...x...y, or y...x...y...x. We compute each case seperately and add up answer.
Lets do the first case then the second case is just the array reversed.
For every y we calculate x, then we process into segtree. For every right y, we can only the right x to be inside the range ly...ry. This way we know the left range must be smaller than ly which means they intersect since we cannot go past ry. Then we must account for the case where left range is bigger than ly, so for the left range we add -1 to segtree and right range we add 1. If in the range ly...ry it only contains right range x, the ans++. Else, it must contain both so we dont count it so the answer stays teh same(1-1 = 0 so we add 0).
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
    int n,q;cin>>n;
    vi v;
    segtree st; // create segtree
    st.init(2*n); // initialize segtree to size n
    int vis[n+1]; // starting point of segment
    rep(i,0,2*n){
        int u;cin>>u;v.pb(u);
        vis[u]=-1;
        
    }
    // the segtree is originally 0 since it represents the number of each value
    int ans[n+1];
    rep(i,0,2*n){
        if(vis[v[i]] >= 0){ // process segment
            ans[v[i]] = st.dfs(vis[v[i]], i); // query sum of all elements from v_i to
            st.set(vis[v[i]],-1); // all values distinct(v is permutation)
            st.set(i,1); // all values distinct(v is permutation)
        }
        else{
            vis[v[i]]=i; // mark start point
        }
    }
    segtree tst; // new segtree for right to left
    tst.init(2*n);
    //reset
    rep(i,1,n+1){
        vis[i]=-1;
    }
    for(int i = 2*n-1;i>=0;i--){
        if(vis[v[i]] >= 0){ // process segment
            ans[v[i]] += tst.dfs(i, vis[v[i]]); // query sum of all elements from v_i to
            tst.set(vis[v[i]],-1); // all values distinct(v is permutation)
            tst.set(i,1); // all values distinct(v is permutation)
        }
        else{
            vis[v[i]]=i; // start point
        }
    }
    rep(i,1,n+1){
        cout<<ans[i]<<" ";
    }
}

```
**Example 8:** https://codeforces.com/edu/course/2/lesson/4/3/practice/contest/274545/problem/E
**Range update problem:** 
This problem is pretty much halbale stacking, but now you must calculate prefix sum(to add all value l...r with x) and query the value of a single element both in logn time.
We can achieve calculating prefix sum using segtree.
To process the first query, we just do the same thing for prefix sums and add x to l and subtract x from r(since we add x to all in range l...r-1).
To process the second query, we just calculate the sum of all values from 1...i. If i is included in some range, we will add that value x to the sum. Otherwise, we sum up + x - x = 0 if i is not in that range.
```cpp
// SEGTREE TO QUERY SUM 
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
    segtree st;
    st.init(n+1);// we start off with 0s
    while(q--){
        int c;cin>>c;
        if(c==1){
            ll l,r,x;cin>>l>>r>>x;
            ll left = st.dfs(l,l+1); // get left value node
            ll right = st.dfs(r,r+1); // get right value node
            st.set(l,left+x); // make it the same value as in array
            st.set(r,right-x); // subtract
           
        }
        else{ // c == 2
            int i;cin>>i;
            cout<<st.dfs(0,i+1)<<endl; // prefix sum of position i = curr value
        }
    }
    
}

```

**Example 9:** https://codeforces.com/edu/course/2/lesson/4/4/practice/contest/274684/problem/A
Query segtree sums but signs alternate.

We can just set the element at index 0 as +, index 1 as -, and alternate elements.So, every even indexed element is positive while every oddindexed element is negative Then, for teh first query we update the value with signs based on its index. If it is even, we make it positive, else we make it negative.
For the second query, we look at the left bound. Since the left border element must be positive, and each proceeding element must alternate signs, we look at if the left border is negative in segment tree. If it is, then teh segment tree sum is -a+b-c+... so we must flip the signs of every element, which means we multiply the whole sum by -1. Else if the first element is positive we just have a-b+c... so we just print it out normally(multiply by 1)

```cpp
// SEGTREE TO QUERY SUM 
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
                // we have to minus or plus. We set index 0 is +, then 1 is -, and alternate
                if(lx%2==0){ // even index +
                    sum[x] = a[lx];
                }
                else{ // odd index -
                    sum[x] = -a[lx];
                }
                
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
    int n,q;cin>>n;
    segtree st;
    st.init(n+1);
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    st.build(v);
    
    cin>>q;
    while(q--){
        int c;cin>>c;
        if(c==0){
            int u,i; cin>>i>>u;
            if((i-1)%2==0){ // 1 indexed, si we have to adjust
                st.set(i-1,u);
            }
            else{
                st.set(i-1,-u);
            }
        }
        else{ // c == 2
            int l,r;cin>>l>>r;
            int sign = 1;
            if(st.dfs(l-1,l) < 0){ // if the first number is negative, we have to reverse signs
                sign = -1;
            }
            cout<<sign * st.dfs(l-1,r)<<endl; // input is 1 indexed
        }
    }
    
}
```
**Example 10:** https://codeforces.com/edu/course/2/lesson/4/4/practice/contest/274684/problem/B

Multiplying two matrix together just means:
|a b| |e f|   |ae+bg  af+bh|
|c d| |g h| = |ce+dg  cf+dh|
This function is associative, so we can use segtrees. This is just normal segtree sum but we replace our operation with this multiplying function. 
We note that the base case(cancel case where we dont count it) is the matrix
|1 0|
|0 1|
since any matrix multiplied by this will return the original matrix
```cpp
// SEGTREE TO QUERY SUM 
struct segtree{
    int sz;
    vector <tuple<ll,ll,ll,ll>> sum; // store sum of nodes
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,{0LL,0LL,0LL,0LL}); // create empty segtree size 2*sz and fill with 0s
    }
    tuple<ll,ll,ll,ll> addd(tuple<ll,ll,ll,ll> a, tuple<ll,ll,ll,ll> b){ // returns product of two matrix
        // multiply 2x2 matrix
        // tuple<(0,0), (0,1), (1,0), (1,1)>
 
        ll w = add(mult(get<0>(a), get<0>(b)), mult(get<1>(a), get<2>(b))); // top left
        ll x = add(mult(get<0>(a), get<1>(b)), mult(get<1>(a), get<3>(b))); // top right
        ll y = add(mult(get<2>(a), get<0>(b)), mult(get<3>(a), get<2>(b))); // bottom left
        ll z = add(mult(get<2>(a), get<1>(b)), mult(get<3>(a), get<3>(b))); // bottom right
        return {w,x,y,z};
    }
    void build(vector <tuple<ll,ll,ll,ll>> &a){
        build(a,0,0,sz);
    }
    void build(vector <tuple<ll,ll,ll,ll>> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
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
        // add
        sum[x] = addd(sum[2*x+1], sum[2*x+2]); // multiplying funciton
        
    }
    
    tuple<ll,ll,ll,ll> dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    tuple<ll,ll,ll,ll> dfs(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return {1,0,0,1}; // BASE CASE MATRIX: DOING NOTHING TO ANS
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        tuple<ll,ll,ll,ll> osa = dfs(l,r,2*x+1,lx,m);
        tuple<ll,ll,ll,ll> nosa = dfs(l,r,2*x+2,m,rx);
        return addd(osa, nosa); // multiplying function
    }
 
};
 
void solve(){
    int n,q;cin>>MOD>>n>>q;
    segtree st;
    st.init(n+1);
    vector <tuple<ll,ll,ll,ll>> v;
    rep(i,0,n){
        ll a,b,c,d;cin>>a>>b>>c>>d;
        v.pb({a,b,c,d});
    }
    st.build(v);
    while(q--){
        int l,r;cin>>l>>r;
        tuple<ll,ll,ll,ll> ans = st.dfs(l-1,r); // input is 1 indexed
        cout<<get<0>(ans)%MOD<<" "<<get<1>(ans)%MOD<<"\n";
        cout<<get<2>(ans)%MOD<<" "<<get<3>(ans)%MOD<<"\n";
        cout<<endl;
    }
    
}
```
**Problem 11:** https://codeforces.com/edu/course/2/lesson/4/4/practice/contest/274684/problem/C

Since ai <= 40, we can take advantage by going through each pair of numbers (x,y) where x < y and count number of ways such that i < j an ai = y, aj = x. To do this, we count for each range the frequency of each number from 1 to 40. Then, if we merge two ranges we count for each (x,y) pair, freq_y from the left range * freq_x from the right range. This will count every inversion that is a mix of the two ranges, meaning y is from left range and x is from right range, since we brute forced over each y bigger than some x where all values are to the left of x so the inversions is multiplied. 
We then add this to inversion_left(x and y are both from left) and inversion_right(x and y are both from right) to get the total inversion for this range. To keep track of frequency, we create a vector. In this implementation, position 1...40 represents the frequency of each value as we go through segtree, while position 0 represent the total inversion. After querying segtree, we just return v_0.


```cpp
// SEGTREE TO QUERY SUM 
struct segtree{
    int sz;
    vector <ll> sum; // number of inversions for each node
    vector <ll> freq[41]; // frequency number x in node 
    
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        sum.assign(2*sz,0LL); // create empty segtree size 2*sz and fill with 0s
        rep(i,0,41){ // set everything to 0
            freq[i].assign(2*sz,0LL);
        }
    }
    void build(vector <int> &a){
        build(a,0,0,sz);
    }
    void build(vector <int> &a, int x, int lx, int rx){ // optimize: build segtree in linear time
        if(rx - lx == 1){
            if(lx < (int)a.size()){ // number is inside arr
                sum[x] = 0; // reset sum = 0 since we only have 1 value
                freq[a[lx]][x]=1; // there is exactly 1 value of a[lx]
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
        ll inv = 0;
        // mixed inversions
        rep(i,1,41){
            rep(j,1,i){
                inv += freq[i][2*x+1] * freq[j][2*x+2]; // x in the first half to y in the second half
            }
        }
        rep(i,1,41){ // merge two nodes(sum their frequencies for each x)
            freq[i][x] = freq[i][2*x+1] + freq[i][2*x+2];
        }
        sum[x] = sum[2*x+1] + sum[2*x+2] + inv;
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            rep(i,0,41){ // reset
                freq[i][x]=0;
            }
            freq[u][x]=1; // set this to 1 instead
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
        ll inv = 0;
        rep(i,1,41){
            rep(j,1,i){
                inv += freq[i][2*x+1] * freq[j][2*x+2]; // x in the first half to y in the second half
            }
        }
        rep(i,1,41){ // merge two nodes(sum their frequencies for each x)
            freq[i][x] = freq[i][2*x+1] + freq[i][2*x+2];
        }
        sum[x] = sum[2*x+1] + sum[2*x+2] + inv;
    }
    vector<ll> dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    vector<ll> dfs(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            // BASE CASE: everything is 0
            vector <ll> v;
            rep(i,0,41){
                v.pb(0);
            }
            return v;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            // everything is inside, so ans is just curr node vector
            vector <ll> v;
            v.pb(sum[x]); 
            rep(i,1,41){
                v.pb(freq[i][x]);
            }
            return v;
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        vector <ll> osa = dfs(l,r,2*x+1,lx,m); // left_inversions
        vector <ll> nosa = dfs(l,r,2*x+2,m,rx); // right_inversions
        ll inv = 0;
        ll cnt = 0;
        // calculate number of "mixed" inversions
        rep(i,1,41){
            inv += osa[i]*cnt;
            cnt += nosa[i];
            /*
            rep(j,1,i){
                inv += osa[i] * nosa[j]; // x in the first half to y in the second half
            }
            */
        }
        
        vector <ll> v;
        v.pb(osa[0] + nosa[0] + inv); // ans
        rep(i,1,41){ // merge two nodes(sum their frequencies for each x)
            v.pb(osa[i] + nosa[i]);
        }
        return v;
    }
 
};
 
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(n+1);
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    st.build(v);
    while(q--){
        int c;cin>>c;
        if(c==1){
            int l,r;cin>>l>>r; 
            vector <ll> v = st.dfs(l-1,r);
            cout<<v[0]<<endl; 
        }
        else{ // c == 2
            int i,u;cin>>i>>u;
            st.set(i-1,u); // input is 1 indexed
        }
    }
    
}
```

**Problem 12:** https://codeforces.com/edu/course/2/lesson/4/4/practice/contest/274684/problem/D
We can use binary to represent whether each element occurs in this range since the elements are only 1...40, so at most 2^40 so ll needed.
If we are at a single element x, we just set the xth bit to 1: 1<<x. Else we merge the set of two ranges by doing OR operation on their binary. This will make sure the set contains union of the sets of the nodes below it. Then the answer is just the number of values in the set of the final range, meaning the number of 1s in its binary representation.

```cpp
struct segtree{
    int sz;
    vector <ll> sum; // number of inversions
    
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
                sum[x] = 1LL<<(a[lx]); // there is 1 value of a[lx], set a[lx]th bit to 1
            }
            // else we just fill with 0 so dont do anything
            return;
        }
        int m = (lx + rx)/2;
        build(a,2*x+1,lx,m);
        build(a,2*x+2,m,rx);
       
        sum[x] = (sum[2*x+1]|sum[2*x+2]); // merge
    }
    void set(int i, ll u){
        set(i,u,0,0,sz);
    }
    void set(int i, ll u, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            sum[x] = 1LL<<(u); // set only 1 to u(changes value in bit representation)
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
        sum[x] = (sum[2*x+1]|sum[2*x+2]);
    }
    ll dfs(int l, int r){
        return dfs(l,r,0,0,sz);
    }
    ll dfs(int l, int r, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return 0; // base case
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return sum[x];
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        ll osa = dfs(l,r,2*x+1,lx,m);
        ll nosa = dfs(l,r,2*x+2,m,rx);
 
        return (osa|nosa); // OR
    }
 
};
 
void solve(){
    int n,q;cin>>n>>q;
    segtree st;
    st.init(n+1);
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    st.build(v);
    while(q--){
        int c;cin>>c;
        if(c==1){
            int l,r;cin>>l>>r; 
            cout<<__builtin_popcountll(st.dfs(l-1,r))<<endl; // count number of 1s(number of distinct occurances)
        }
        else{ // c == 2
            int i,u;cin>>i>>u;
            st.set(i-1,u); // input is 1 indexed
        }
    }
    
}
```
## BIT/Fenwick Tree
Same operations as segtree, just different impl.
Common technique unrelated: Given that steps of an operation can be both x and n/x, it is best to set x = sqrt(n) to minimize worst case.

### Implementation
For every position $i$, the interval that it encompasses is the interval that ENDS AT $i$ and has length of x = the maximum power of 2 that divides $i$. Note $i$ is 1 indexed. So, $fenwick[pos] = sum[i-x+1,i].$ Therefore, any range $[1...n]$ can be divided into logn subranges.


To calculate x, we use a math trick: 
Represent that number in binary, the maxium power of 2 that divides $i$ is at the position of the last 1 bit. After finding the position the length is then $2^{pos}$. 
We use a math trick to find position:$pos = i & -i$.Then $x=(i & -i)$.

We can interpret this set of intervals as a tree, since all intervals are disjoint(not intersecting) or some interval completely contains another.

Note the father of pos $i$ always has a bigger index than $i$. Moreover, the father of interval at position $i$ is just $i$ length of interval $i$ = $i+x = i + (i&-i).$
Thus, we can update elements in O(logn) time by updating its parents using this calculation.

```cpp
void update(int pos, int val) { // updates position with new value(val = new_value - original value)
    while (pos <= N) { // while we havent gone out of BIT
        fenwick[pos] += val; // add value
        pos += (pos&-pos); // go to its parent
    }
}
```

We can also query sum from range $[1...i]$. We do this by first considering some position $i$ and calculate the range of that([i-x...i]). Then, we decrease our pointer so that we calculate the range starting at $i-x$ and then we just sum the whole thing up until we reach index 0.
```cpp
int query(int pos) { // calculates sum from 1...pos
   int sum = 0;
   while (pos > 0) { 
      sum += fenwick[pos]; // adds to sum
      pos -= lsb(pos); // decreases itr so it goes to the first position whose sum we havent calculated
   }
   return sum;
}
```
**Both operations work in O(logn)**

Both BIT and Segtrees are based on consecutive index of elements. Thus, we must first compress the index of our elements if necessary before putting them into the data structure.

## Ordered Sets

Include the following lines to have ordered multiset where we can use two functions both logn:
- find_by_order(): returns iterator to its sorted position/the kth largest element in set(starts at 0)
- order_of_key(): returns number of items in set strictly smaller than it(index position if elements are distinct)
```cpp
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/tree_policy.hpp>
using namespace __gnu_pbds;
template<typename T>
using orderedMultiset = tree<T ,null_type,std::less_equal<T>, rb_tree_tag,tree_order_statistics_node_update>;

int main(){
  orderedMultiset <int> ms;
  auto it = ms.find_by_order(0);
  auto itr = ms.order_of_key(0);
  

}
```
**Reference:** https://www.geeksforgeeks.org/policy-based-data-structures-g/

NOTE: This is multiset. For set, use less<T> instead of less_equal<T>.
WARNING: Ordered sets works fine, but prob shouldnt use less_equal for orderedmultiset because find() and erase might not work and will be slow. I think from cf blog it also says lower_bound and upper_bound are switched, so we should use ms.erase(ms.upper_bound(val));
Thus, to implement ordered multiset we just use set <pair<int,int>> like GHG article described above.
**Alternative Solution for inversion counting:** https://www.spoj.com/problems/INVCNT/ using ordered sets:
```cpp
void solve(){
    int n,q;cin>>n;
    vi v;
    orderedMultiset <int> ms;
    ll ans = 0;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        ms.insert(u);
        ans += ms.size() - ms.order_of_key(u+1); // count number of elements that came before curr element that is bigger than curr element.
    }
    cout<<ans<<endl;
    
}

```

**Problem 5:** https://codeforces.com/contest/1181/problem/D

There are 3 things that make it not approachable. A brute force solution obviously TLEs. We will use "coord compression" in a sense where we can calculate the cities that are in rotation at any point in time. Thus when we get some time for query, we can see at that time how many cities are already in rotation and what they are ig.
Second thing is we have to get the specific city number. To do this we can use **orderedmultiset** that stores all current cities in order. Then, we perform time calculation mod # of cities in array to determine which city hosts on this day.
Third, this part was the one that actually took thinking: Adding cities using orderedmultisets naively for every query gets TLE. 
We note that adding cities is monotonic increasing as time increases. Thus, we **dont process per query**. Instead we get all queries, sort by time, then add extra cities only if we need and as we go up. This way, we never add same city to multiset twice. After this we just fill answer to its previous query position, then output.

```cpp
void solve(){
    int n,m,q;cin>>n>>m>>q;
    int cnt[m+1]={0};
    rep(i,0,n){
        int u;cin>>u;cnt[u]++;
    }

    vector <pair<ll,ll>> v;
    rep(i,1,m+1){
        v.pb({cnt[i],i});
    }
    sort(v.begin(),v.end()); // sort by # of times city hosted
    vector <pair<ll,ll>> a; // (x,y) - at time x, we will have the first y cities in the array, and we'll get position using mod accordingly
    // calculating x and y
    ll osa = 0;
    rep(i,1,m){
        if(v[i].first != v[i-1].first){ // lowest
            a.pb({(osa), i}); 
            osa = (v[i].first-v[i-1].first)*i;
        }
    }
    a.pb({(osa), m}); // lastly well have osa days have passed and we now have all cities in array
    rep(i,1,a.size()){ // prefix sum for total number of days past
        a[i].first += a[i-1].first; 
    }
    vector<pair<ll,ll>> srt;
    ll ans[q]; // store query answers
    rep(i,0,q){ 
        ll k;cin>>k;srt.pb({k,i});
    }
    sort(srt.begin(),srt.end());// sort queries so we can process in order and thus not add to multiset every time which TLE
    orderedMultiset<ll> ms;
    rep(i,0,q){
        ll k = srt[i].first;
        k-=n; pair<ll,ll> find = {k,0};
        auto it = upper_bound(a.begin(),a.end(),find);
        --it;
        pair<ll,ll> nosa = *it;
        ll md = (k-nosa.first-1+nosa.second)%nosa.second; // get mod for position
        rep(i,ms.size(),nosa.second){ // add nessary cities if needed
            ms.insert(v[i].second);
        }
        auto itr = ms.find_by_order(md);
        ans[srt[i].second]=*itr;
    }
    rep(i,0,q){
        cout<<ans[i]<<"\n";
    }
}   
```


