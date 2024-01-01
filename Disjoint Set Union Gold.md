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
  if(x==y){ // same leader
    return; // skip since they are already connected
  }
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
**Example 2:** https://codeforces.com/edu/course/2/lesson/7/1/practice/contest/289390/problem/B
We can perform these function in a set and get the answer using dfs. So we can find sum, min, max of sets and join them together. To do this we only need to modify union. For each function, we will union the two sets together and perform that function.
```cpp
int rdsu[300005]; // stores rank of leader based on size of set
int pdsu[300005]; // stores parent of each node
int mn[300005]; // stores minimum element for each set
int mx[300005]; // max element
int tot[300005]; // stores total element of each set
 
int get(int x){
    if(x != pdsu[x]){
        pdsu[x] = get(pdsu[x]); // set parent[x] as the root
    }
    return pdsu[x]; // else we know this is root
}
 
void unite(int x, int y){
    x = get(x);
    y = get(y);
    if(x == y){ // same leader
        return; // skip since they are already connected
    }
    if(rdsu[x] == rdsu[y]){ // two sets equal size
        rdsu[x]++; // break equality so set y connects to x
    }
    if(rdsu[x] > rdsu[y]){ // x has bigger size
        pdsu[y] = x; // connect root y with x
        tot[x] += tot[y]; // add total of y to x since y -> x
        mn[x] = min(mn[x], mn[y]); // calc min of union
        mx[x] = max(mx[x], mx[y]); // calc max
    }
    else{
        pdsu[x] = y; // connect x to y
        tot[y] += tot[x]; // add total of x to y since its connected
        mn[y] = min(mn[y], mn[x]); // calc min
        mx[y] = max(mx[y], mx[x]); // calc max
    }
  
}
 
void solve(){
    int n,q;cin>>n>>q;
    // reset
    rep(i,1,n+1){
        rdsu[i]=0; // at the start all ranks are 0 since all size = 1
        pdsu[i]=i; // at the start every element is in a set by themselves
        tot[i]=1; // initally, number of elements in each set is 1
        mn[i]=i; // min is i
        mx[i]=i; // max is also i
    }
    while(q--){
        string s;cin>>s;
        if(s=="union"){
            int x,y;cin>>x>>y;
            unite(x,y);
        }
        else{
            int x;cin>>x;
            x = get(x); // get leader
            cout<<mn[x]<<" "<<mx[x]<<" "<<tot[x]<<endl;
        }
    }
}   
```
**Example 3:** https://codeforces.com/edu/course/2/lesson/7/1/practice/contest/289390/problem/C
We Note that DSU is just traversing up a tree. For add, we just add the experience to the leader of the set. Let the answer be the sum of values from current node to the root(leader) node. To calculate experience, we traverse up the tree and add the value of each node.
For unite(), we want to add an edge connecting smaller leader to bigger leader. We know that any node in the smaller set must go through the small leader. If we combine with big leader, then we DO NOT want all the previous experiences added to big leader. Thus, we just subtract the previous of the big leader from the small leader then connect them. Therefore, we avoid overcounting since every path inside small leader set will not count the previous experience by the bigger set and everytime we add experience to the big leader(leader of this combined set), we add experience to the whole thing.

```cpp
int rdsu[300005]; // stores rank of leader based on size of set
int pdsu[300005]; // stores parent of each node
int sum[300005]; // experience points for each node
 
 
int get(int x){ // get leader
    while(x != pdsu[x]){
        // dont path compress cause we want to calculate sum
        x = pdsu[x];
    }
    return x;
}
 
void unite(int x, int y){
    x = get(x);
    y = get(y);
    if(x == y){ // same leader
        return; // skip since they are already connected
    }
    if(rdsu[x] == rdsu[y]){ // two sets equal size
        rdsu[x]++; // break equality so set y connects to x
    }
    if(rdsu[x] > rdsu[y]){ // x has bigger size
        pdsu[y] = x; // connect root y with x
        int prev = sum[x]; // previously acculumlated experience of big set
        sum[y] -= prev; // adjust since we are summing up path from node to leader
        // this is so when we go up the path, we do not count prev
 
    }
    else{
        pdsu[x] = y; // connect x to y
        int prev = sum[y];
        sum[x] -= prev; // adj, previous experience does not belong to added set
    }
  
}
 
void solve(){
    int n,q;cin>>n>>q;
    // reset
    rep(i,1,n+1){
        rdsu[i]=0; // at the start all ranks are 0 since all size = 1
        pdsu[i]=i; // at the start every element is in a set by themselves
        sum[i]=0; // initally, every node has 0 experience
        
    }
    while(q--){
        string s;cin>>s;
        if(s=="join"){ // unite two nodes
            int x,y;cin>>x>>y;
            unite(x,y); // join two nodes
        }
        else if(s=="add"){
            int x,y;cin>>x>>y; // node, value
            // add experience to leader of node x
            while(x != pdsu[x]){ // going up
                x = pdsu[x];
            }
            sum[x]+=y; // add experience to leader of x
        }
        else{
            int x;cin>>x;
            int ans = 0; // sum of experience
            while(x != pdsu[x]){ // going up
                ans += sum[x];
                x = pdsu[x];
            }
            ans += sum[x]; // adds leader experience
            cout<<ans<<endl;
        }
    }
    
}   
```

**Example 4:** https://codeforces.com/edu/course/2/lesson/7/1/practice/contest/289390/problem/D
This is pretty much "reverse DSU". At the start some nodes are connected and we are erasing edges instead of adding them.
Because we are **only answering queries**, it is possible to solve this by **processing queries in reverse order**. 

```cpp
int rdsu[300005]; // stores rank of leader based on size of set
int pdsu[300005]; // stores parent of each node
int sum[300005]; // experience points for each node
 
 
int get(int x){ // get leader
    while(x != pdsu[x]){
        // dont path compress
        x = pdsu[x];
    }
    return x;
}
 
void unite(int x, int y){
    x = get(x);
    y = get(y);
    if(x == y){ // same leader
        return; // skip since they are already connected
    }
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
    int n,m,q;cin>>n>>m>>q;
    // reset
    rep(i,1,n+1){
        rdsu[i]=0; // at the start all ranks are 0 since all size = 1
        pdsu[i]=i; // at the start every element is in a set by themselves        
    }
    rep(i,0,m){
        int x,y;cin>>x>>y;
        
    }
    vector <tuple<string,int,int>> v;
    while(q--){
        string s;cin>>s;
        int x,y;cin>>x>>y;
        v.pb({s,x,y});
        
    }
    reverse(v.begin(),v.end()); // reverse process query
    vector <string> ans;
    rep(i,0,v.size()){
        if(get<0>(v[i]) == "ask"){
            if(get(get<1>(v[i])) == get(get<2>(v[i]))){
                ans.pb({"YES"});
            }
            else{
                ans.pb({"NO"});
            }
        }
        else{ // unite
            unite(get<1>(v[i]), get<2>(v[i]));
        }   
    }
    reverse(ans.begin(),ans.end()); // reverse answer
    rep(i,0,ans.size()){
        cout<<ans[i]<<"\n";
    }
}   
 
```

**Example 5:** 
