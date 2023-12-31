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
int get(int x){
  if(a != parent[a]){
    parent[a] = get(parent[a]); // set parent[a] as the root
  }
  return a; // else we know this is root
}
  
void union(int x, int y){
  x = get(x);
  y = get(y);
  parent[x] = y;
}
```

From Source: get and union work in O(α(m,n)) time amortized, where α(m,n) is the inverse Ackermann function, m is the number of performed operations get and n is the number of elements. This is around log*(n) which works like this:

Let n = 2^20. Then the algorithm will terminate if we reach anything < 1. The steps are:
2^20 -> 20 = 2^4. -> 4. = 2^2. -> 2. = 2^1. -> 1. = 2^0. -> 0.smth then we terminate. We can see we only took like 5 steps.

Thus, **algorithm works in constant time** for any reasonable n.



