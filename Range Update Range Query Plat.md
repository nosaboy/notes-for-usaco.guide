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
- Assign all values from l to r-1 as x
- Get the value at position i

This means we must maintain the order of operations: Applying function x after y differs from y after x.
Lazy propagation technique is as follows: we store all updates on a node in some other array called lazy. To save time, we will not actually update the array until we get a query that uses this range. Thus, we will store the updates in lazy based on its time -> store in the node. The higher the node, the more recent the update is. Thus, all we have to do to do get() is just to walk up from the node to the root and find the most recent updates and perform it. 
When we update/do an operation, it will become the current most recent operation. Say we want to perform this operation on node x(segment that covers this range). Since this is the most recent operation, we the to be the last lazy value(last update we'll make) on the path from this node to root. Thus, we must clear out the path by pushing all lazy values from this path to the other node.
Let $y$ be some lazy update value present on a node in this path. We will push this y to both its children, thus clearing the lazy value itself. We will do this until we reach node x, the node we want to update. Since our current update will trump the $y$ update that was previously pushed onto us, we will just update y using x for this node. For all other nodes, we push it down to its child so that we clear the lazy value of the node while maintaining the updates if we still want to go from that node to root - peform get().
All in all, our lazy updates allows us to:
- Apply operation to node x
- Clear all nodes that are on the path from x to root, while maintaining the order of the functions applied so that for any node, the path to the root will have the same ordering of applying function.

The implementation is simple. For the assign function, we will go through segtree normally and update nodes. However, we want to clear all nodes that are in the path from x to root. We do this by **pushing its current lazy value/function down to its two children**, so that one of the child keeps its ordering while we go down the other child and eventually come to x, in which we can perform x after y.

```cpp

```


