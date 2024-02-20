### CF EDU: https://codeforces.com/edu/course/2/lesson/5

**Problem 1:** 
Query two operations:
- add(l,r,v): add v to all numbers [l...r)
- get(i): return $a[i]$

If we add some value v to range l...r-1, we want to maintain which range we add.
For every segment/node in segtree:
- If this segment is completely inside the desired segment, and add v to this node.
- Else we go and visit left and right, and eventually stop and add v.

Then, to get value $a[i]$, we go though the segtree until we reach node at position $i$. The answer is then the sum of all numbers on the path from root to i.


```cpp
struct segtree{
    int sz;
    vector <ll> val; // store sum of nodes
 
    // CODE FOR IMPL
 
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,0); // create empty segtree size 2*sz and fill with 0s
    }

    void add(int l, int r, int v){
        add(l,r,v,0,0,sz);
    }
    void add(int l, int r, int v, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            val[x] += v;
            return;
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        add(l,r,v,2*x+1,lx,m);
        add(l,r,v,2*x+2,m,rx);
    }
        
    ll get(int i){
        return get(i,0,0,sz);
    }

    ll get(int i, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            return val[x];
        }
        int m = (lx+rx)/2; // mid
        ll ans = 0;
        if(i < m){ // if desired position < mid, we go to left subtree
            ans = get(i, 2*x+1,lx,m);
        }
        else{ // we go to right subtree
            ans = get(i, 2*x+2,m,rx);
        }
        return val[x] + ans;
    }
};
 

void solve(){
    int n,q;cin>>n>>q;
    segtree st; st.init(n);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int l,r,v;cin>>l>>r>>v;
            st.add(l,r,v);
        }
        else{
            int i;cin>>i;
            cout<<st.get(i)<<endl;
        }
    }
}   

```

**Problem 2:** https://codeforces.com/edu/course/2/lesson/5/1/practice/contest/279634/problem/B

The above code does range update point query for all associative and commutative operations such as gcd/lcm, xor, add/multiply, max/min, etc.

SEGTREE CODE FOR MAX:
```cpp
struct segtree{
    int sz;
    vector <ll> val; // store sum of nodes
    
    // operation MUST BE associative and commutative
    ll operation(ll a, ll b){
        return max(a,b);
    }
    // CODE FOR IMPL
    
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,0); // create empty segtree size 2*sz and fill with 0s
    }

    void add(int l, int r, int v){
        add(l,r,v,0,0,sz);
    }
    void add(int l, int r, int v, int x, int lx, int rx){ // sum of segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            val[x] = operation(val[x], v);
            return;
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        add(l,r,v,2*x+1,lx,m);
        add(l,r,v,2*x+2,m,rx);
    }
        
    ll get(int i){
        return get(i,0,0,sz);
    }

    ll get(int i, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(rx-lx==1){ // current node is a leaf
            return val[x];
        }
        int m = (lx+rx)/2; // mid
        ll ans = 0;
        if(i < m){ // if desired position < mid, we go to left subtree
            ans = get(i, 2*x+1,lx,m);
        }
        else{ // we go to right subtree
            ans = get(i, 2*x+2,m,rx);
        }
        return operation(val[x], ans);
    }
};
 
```
### Lazy Propagation
**Problem 3:** https://codeforces.com/edu/course/2/lesson/5/1/practice/contest/279634/problem/C
We want to apply a **assosicative** but **non-communiative** operation on segtree. One example is querying following operations:
- Modify/Assign all values from l to r-1 as x
- Get the value at position i

This means we must maintain the order of operations: Applying function x after y differs from y after x.
Lazy propagation technique is as follows: we store all updates on a node in some other array called lazy. To save time, we will not actually update the array until we get a query that uses this range. Thus, we will store the updates in lazy based on its time -> store in the node. The higher the node, the more recent the update is. Thus, all we have to do to do get() is just to walk up from the node to the root and find the most recent updates and perform it. 
When we update/do an operation, it will become the current most recent operation. Say we want to perform this operation on node x(segment that covers this range). Since this is the most recent operation, we the to be the last lazy value(last update we'll make) on the path from this node to root. Thus, we must clear out the path by pushing all lazy values from this path to the other node.
Let $y$ be some lazy update value present on a node in this path. We will push this y to both its children, thus clearing the lazy value itself. We will do this until we reach node x, the node we want to update. Since our current update will trump the $y$ update that was previously pushed onto us, we will just update y using x for this node. For all other nodes, we push it down to its child so that we clear the lazy value of the node while maintaining the updates if we still want to go from that node to root - peform get().
All in all, our lazy updates allows us to:
- Apply operation to node x
- Clear all nodes that are on the path from x to root, while maintaining the order of the functions applied so that for any node, the path to the root will have the same ordering of applying function.

The implementation is simple. For the assign function, we will go through segtree normally and update nodes. However, we want to clear all nodes that are in the path from x to root. We do this by **pushing its current lazy value/function down to its two children**, so that one of the child keeps its ordering while we go down the other child and eventually come to x, in which we can perform x after y.

**NOTE:** Since doing propagation is only O(1), we can do it on both get() and modify() since time doesnt matter.
```cpp
struct segtree{
    int sz;
    vector <ll> val; // store sum of nodes
    
    long long no = 1000000000000000005; // node does not have operation
    // operation MUST BE associative
    ll operation(ll a, ll b){ // do function b on a(assign b to a)
        if(b==no){ // no operation for b
            return a;
        }
        return b; // else in this case we are replacing a with b( assigning b to a)
    }
    // CODE FOR IMPL
    
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,0); // create empty segtree size 2*sz and fill with 0s
    }
 
    void propagate(int x, int lx, int rx){ // clear node x
        // apply function orders to its two children
        if(rx-lx==1) return; // x is leaf node, doesnt have children
        val[2*x+1] = operation(val[2*x+1],val[x]); // apply "assignment function" to child1 
        val[2*x+2] = operation(val[2*x+2],val[x]); // apply to child2
        val[x]=no; // node x is cleared, thus it has no operation
        // this way we wont care about it when going from node -> root
 
    }
    void modify(int l, int r, int v){
        modify(l,r,v,0,0,sz);
    }
    void modify(int l, int r, int v, int x, int lx, int rx){ // modify segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        propagate(x,lx,rx); // at every instance in the path until we reach desired node, 
        // we'll propogate/clear this node to clear path
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            val[x] = operation(val[x], v); // we have found node x, so apply operation
            // after this, we know that all nodes above on the path will be cleared
            return; // thus we stop going down as this is the last operation
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        modify(l,r,v,2*x+1,lx,m);
        modify(l,r,v,2*x+2,m,rx);
    }
        
    ll get(int i){
        return get(i,0,0,sz);
    }
 
    ll get(int i, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        propagate(x,lx,rx); // at every instance in the path until we reach desired node, 
        if(rx-lx==1){ // current node is a leaf
            return val[x];
        }
        int m = (lx+rx)/2; // mid
        ll ans = 0;
        if(i < m){ // if desired position < mid, we go to left subtree
            ans = get(i, 2*x+1,lx,m);
        }
        else{ // we go to right subtree
            ans = get(i, 2*x+2,m,rx);
        }
        return operation(val[x], ans);
    }
};
 
 
void solve(){
    int n,q;cin>>n>>q;
    segtree st; st.init(n);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int l,r,v;cin>>l>>r>>v;
            st.modify(l,r,v);
        }
        else{
            int i;cin>>i;
            cout<<st.get(i)<<endl;
        }
    }
}   
```


**Problem 4:** https://codeforces.com/edu/course/2/lesson/5/2/practice/contest/279653/problem/A

Support the following operation:
- Add v to all elements in range $[l,r)$
- Get min val of all elements in range $[l,r)$

We can just use a normal segtree. Since they are both communiative and associative, we dont have to use lazy propagation. Instead, we update the sum based on val array: what add operations are on this specific node. Eberytime we get the sum we dfs up. 
To get min, we get the min of two sides. Then, we must add on the sum of the operations done on this node.

```cpp
struct segtree{
    int sz;
    vector <ll> val; // store sum of nodes
    vector <ll> mn;
    long long no = 1000000000000000005; // node does not have operation
    // CODE FOR IMPL
    
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,0); // create empty segtree size 2*sz and fill with 0s
        mn.assign(2*sz,0); // assign MAX
    }
 
    void modify(int l, int r, int v){
        modify(l,r,v,0,0,sz);
    }
    void modify(int l, int r, int v, int x, int lx, int rx){ // modify segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
 
        // we'll propogate/clear this node to clear path
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            //perform operation
            val[x]+=v;
            mn[x]+=v;
            return; // break
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        modify(l,r,v,2*x+1,lx,m);
        modify(l,r,v,2*x+2,m,rx);
        mn[x]=min(mn[2*x+1],mn[2*x+2])+val[x]; // min of child + operation of node x
    }
        
    ll get(int l, int r){
        return get(l,r,0,0,sz);
    }
 
    ll get(int l, int r, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return no; // MAX
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return mn[x];
        }
        int m = (lx+rx)/2; // mid
        return min(get(l,r, 2*x+1,lx,m),get(l,r, 2*x+2,m,rx))+val[x];
    }
};
 
```



**Problem 5:** https://codeforces.com/edu/course/2/lesson/5/2/practice/contest/279653/problem/B

We can essentially do the same thing as previous since this modify = "*" and calc = "+" is also non-communiative but associative. 
Make sure to make a build function so that we build it with mn = sum to ensure array has all 1s. Thats why I TLE tcs 9. Then, we just store mn = sum of this segment, val = operation done to this node. For each thing if the desired segment contains curr seg we just return mn = sum. Else we just get the sums of its children by dfs, then we multiply it by its val.
```cpp
struct segtree{
    int sz;
    vector <ll> val; // store multiply of each node
    vector <ll> mn; // store sum of nodes
    long long no = 0; // node does not have operation
    // CODE FOR IMPL
    
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,1); // create empty segtree size 2*sz and fill with 0s
        rep(i,0,2*sz) mn.pb(0); // filled with 0s at the start
    }
    void build(){
        build(0,0,sz);
    }
    void build(int x, int lx, int rx){ // build segtree with everything 1 at the start
        if(rx-lx==1){
            mn[x]=1LL;
            return;
        }

        int m = (lx+rx)/2;
        build(2*x+1,lx,m);
        build(2*x+2,m,rx);
        mn[x]=mult(add(mn[2*x+1],mn[2*x+2]),val[x]);
        //mn[x]=1;
    }
    void display(){
        rep(i,0,2*sz){
            cout<<mn[i]<<" ";
        }
        cout<<endl;
    }
    void modify(int l, int r, int v){
        modify(l,r,v,0,0,sz);
    }
    void modify(int l, int r, int v, int x, int lx, int rx){ // modify segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            //perform operation
            val[x]=mult(val[x],v);
            mn[x]=mult(mn[x],v);
            return; // break
        }
        // else dfs through left and right node
        int m = (lx+rx)/2; 
        modify(l,r,v,2*x+1,lx,m);
        modify(l,r,v,2*x+2,m,rx);
        mn[x]=mult(add(mn[2*x+1],mn[2*x+2]),val[x]);
    }
        
    ll get(int l, int r){
        return get(l,r,0,0,sz);
    }

    ll get(int l, int r, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return no; 
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return mn[x];
        }
        int m = (lx+rx)/2; // mid
        return mult(add(get(l,r, 2*x+1,lx,m),get(l,r, 2*x+2,m,rx)),val[x]); // find sum, then multiply
    }
};
 

void solve(){
    int n,q;cin>>n>>q;
    segtree st; st.init(n);
    st.build();
    while(q--){
        int op;cin>>op;
        //st.display();
        if(op==1){
            int l,r,v;cin>>l>>r>>v;
            st.modify(l,r,v);
        }
        else{
            int l,r;cin>>l>>r;
            cout<<st.get(l,r)<<endl;
        }
       
    }
}   

```

**Problem 6:** https://codeforces.com/edu/course/2/lesson/5/2/practice/contest/279653/problem/c

This is also same cause both functions are communiative and associative.
Thus, we can just change up our previous code so that modify is | instead of * and calc is & instead of +.
The only thing that differs is that for every update/modify on range, we have to add it to the segement based on its size, since we're updating 

**Problem 7:**
Same thing but we just have to account for length since we're adding some v to all values.

**Problem 8:** https://codeforces.com/edu/course/2/lesson/5/2/practice/contest/279653/problem/E

Since assigning a value is non-communative, we have to use **lazy propagation**. Since assignment and min are both associative and distributive, we can do so.

**Problem 9:** https://codeforces.com/edu/course/2/lesson/5/2/practice/contest/279653/problem/F

This is same as above except we have sum, so we have to accomodate for len.
For each node, we will propagate first, then go in and calculate node value. This will make curr val_x = no operation.
To propagate, we will for every node, push the val to its two child nodes, then update their answers accordingly since it will be in order, so on the way back the operation will be applied anyway. We then make curr operation = no operation.
After, we will go back up on some segment like normal.

```cpp
struct segtree{
    int sz;
    vector <ll> val; // store multiply of each node
    vector <ll> mn; // store sum of nodes
    long long no = 1000000000000000005; // node does not have operation
    long long neutral = 0; // wont affect sum
    // CODE FOR IMPL
    long long mod_op(long long a, long long b,int len){ // modify function
        if(b==no) return a;
        return b*len;

    }

    long long calc_op(long long a, long long b){ // calc function
    
        return a + b;
    }

    void apply_mod_op(long long &a, long long b, int len){
        a = mod_op(a,b,len);
    }
    void init(int n){ // create empty segtree with length at least n increased to closest power of 2(for leaves of binary tree)
        sz = 1;
        while(sz < n){
            sz *= 2;
        }
        val.assign(2*sz,0); // create empty segtree size 2*sz and fill with 0s
        mn.assign(2*sz,0); // filled with 0s at the start
    }

    // LAZY PROPAGATION
    void propagate(int x, int lx, int rx){
        if(rx-lx==1) return; // dont propagate leaf
        int m = (lx+rx)/2;
        // move operation to child node ans
        // move 
        apply_mod_op(val[2*x+1],val[x],1); 
        apply_mod_op(val[2*x+2],val[x],1); 
        apply_mod_op(mn[2*x+1],val[x],(rx-m)); 
        apply_mod_op(mn[2*x+2],val[x],(m-lx)); 
        val[x]=no;
    }
 
    void modify(int l, int r, int v){
        modify(l,r,v,0,0,sz);
    }
    void modify(int l, int r, int v, int x, int lx, int rx){ // modify segement [l...r)
        // l and r = desired range, x = current node, lx and rx = current range
        propagate(x,lx,rx);
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return;
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            //perform operation
            apply_mod_op(val[x],v,1);
            apply_mod_op(mn[x],v,(rx-lx)); // apply to entire range
            return; // break
        }
        // else dfs through left and right node
        int m = (lx+rx)/2;
        modify(l,r,v,2*x+1,lx,m);
        modify(l,r,v,2*x+2,m,rx);
        mn[x]=calc_op(mn[2*x+1],mn[2*x+2]);
        // dont have to apply mod to mn since we alredy propagated
    }
        
    ll get(int l, int r){
        return get(l,r,0,0,sz);
    }

    ll get(int l, int r, int x, int lx, int rx){ // assign ith element to u and update sums above it
        // i = desired final position(leaf), u = value we want to assign, x = current node, lx, rx = current range of node
        propagate(x,lx,rx);
        if(r <= lx || rx <= l){ // curr range is outside of desired range entirely
            return neutral; 
        }
        if(l <= lx && rx <= r){ // curr range is inside of desired range entirely
            return mn[x];
        }
        int m = (lx+rx)/2; // mid
        return mod_op(calc_op(get(l,r, 2*x+1,lx,m),get(l,r, 2*x+2,m,rx)),val[x],min(rx,r)-max(lx,l));
        // note, we must apply this node to be in range of both segments, which is what 
        // this sum represents. Thus, our left bound must be the max of the two lefts and 
        // right bound is min. This way our segment is contained in both l...r and lx...rx

    }
};
 

void solve(){
    int n,q;cin>>n>>q;
    segtree st; st.init(n);
    while(q--){
        int op;cin>>op;
        if(op==1){
            int l,r,v;cin>>l>>r>>v;
            st.modify(l,r,v);
        }
        else{
            int l,r;cin>>l>>r;
            cout<<st.get(l,r)<<endl;
        }
       
    }
}   

```
