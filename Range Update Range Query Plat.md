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
We want to apply a non communiative operation on segtree. One example is querying following operations:
- Assign all values from l to r-1 as x
- Get the value at position i

This means we must maintain the order of operations: we must apply x before y if x node is further down than y.

