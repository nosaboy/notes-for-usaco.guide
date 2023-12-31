## Usage
**Given some sets each containing some numbers(like connected components of graphs), DSU supports two operation in**:
- get(x): returns the set that contains element x
- union(x,y): unite(combine) {the set with element x} with {the set with element y} into a single set

### Implementation
For each set, we pick a "leader" element as its identifier. This means get(x) = leader_of_set_x for all x in this set.
For union(x,y), we join the two sets by setting leader_of_x as the leader of set y for all elements in y. This way all elements in y will also point of leader_x. Brute force change every node will lead to O(1) get() and O(logn) union().
get(x): return $leader[x]$
union(x,y):
  for i in $leader[x]$
    $leader[i] = leader[y]$

We can speed up to O(1) get() and O(logn) union by adding one simple line: 
if(size[leader[x]] > size[leader[y]]):
  swap(x,y)

Doing this, our union function joins the smallest to the largest: only replaces leader of smaller set. For each element, its leader will get replace at most log(n) times since for an element its set size for the worst case will only be 1 -> 2 -> 4 -> ... since otherwise this set size is the bigger out of the two. Thus each element gets logn replacements on average in **amortization**.

Further Optimization:
We consider a different approach that has a slower get() but not have amortization:
Let each set be a tree where each element is a node and has a parent.
For each get(x) we traverse through its parents until we find the root node: $parent[x] = x$ then return.
For each union(x,y) we find both leaders using get(x) and get(y) then set $parent[leader[x]] = y$ which connects the root of set x to root of y.
This leads to O(n) get() and O(n) union.

get(x):
  while($a != parent[a]$):
    $a = parent[a]$
  return a // root
union(x,y):
  x = get(x)
  y = get(y)
  $parent[x] = y$

We can speed this up using the above trick. We always link the smaller set with the bigger one. The time complexity is number of links we have to go through to find root. This will work in **precisely logn time** for each query.
We can optimize further using path compression. For every node we visit which going up, we set its parent as the root after we found it. This way, we dont have to reiterate through paths for every get(). To do this, we make get(x) recursive:
```cpp
int rdsu[200005]; // stores rank of leader based on size of set
int pdsu[200005]; // stores parent of each node
 
int get(int x){
  if(x != pdsu[x]){
    pdsu[x] = get(pdsu[x]); // set parent[x] as the root
  }
  return pdsu[x]; // else we know this is root
}
 
void unite(int x, int y){
  x = get(x);
  y = get(y);
  if(rdsu[x] == rdsu[y]){ // two sets equal size
    rdsu[x]++; // break equality so set y connects to x
  }
  if(rdsu[x] > rdsu[y]){ // x has bigger size
    pdsu[y] = x; // connect root y with x
  }
  else{
    pdsu[x] = y; // connect x to y
  }
  
}
```

From Source: get and union work in O(α(m,n)) time amortized, where α(m,n) is the inverse Ackermann function, m is the number of performed operations get and n is the number of elements. This is around log*(n) which works like this:

Let n = 2^20. Then the algorithm will terminate if we reach anything < 1. The steps are:
2^20 -> 20 = 2^4. -> 4. = 2^2. -> 2. = 2^1. -> 1. = 2^0. -> 0.smth then we terminate. We can see we only took like 5 steps.

Thus, **algorithm works in constant time** for any reasonable n.

**Example 1:** https://codeforces.com/edu/course/2/lesson/7/1/practice/contest/289390/problem/A
https://judge.yosupo.jp/problem/unionfind: we change up query code to match

DSU queries
```cpp
int rdsu[200005]; // stores rank of leader based on size of set
int pdsu[200005]; // stores parent of each node
 
int get(int x){
  if(x != pdsu[x]){
    pdsu[x] = get(pdsu[x]); // set parent[x] as the root
  }
  return pdsu[x]; // else we know this is root
}
 
void unite(int x, int y){
  x = get(x);
  y = get(y);
  if(rdsu[x] == rdsu[y]){ // two sets equal size
    rdsu[x]++; // break equality so set y connects to x
  }
  if(rdsu[x] > rdsu[y]){ // x has bigger size
    pdsu[y] = x; // connect root y with x
  }
  else{
    pdsu[x] = y; // connect x to y
  }
  
}
 
void solve(){
    int n,q;cin>>n>>q;
    // reset
    rep(i,1,n+1){
        rdsu[i]=0; // at the start all ranks are 0 since all size = 1
        pdsu[i]=i; // at the start every element is in a set by themselves
    }
    while(q--){
        string s;cin>>s;
        int x,y;cin>>x>>y;
        if(s=="union"){
            unite(x,y);
        }
        else{
            if(get(x) == get(y)){
                cout<<"YES\n";
            }
            else{
                cout<<"NO\n";
            }
        }
    }
}  
```
#### Associative and Commutative functions
We can perform these function in a set and get the answer using dfs. So we can find sum, min, max of sets and join them together. To do this we only need to modify union.





