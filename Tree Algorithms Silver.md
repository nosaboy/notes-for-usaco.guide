#### Calculate # of nodes in subtree or distance from node to root using dp
```cpp
int dist[MAX_N]; // distance of node to root
int cnt[MAX_N]; // number of nodes in subtree rooted at n
void dfs(int n){
    vis[n] = true;
    cnt[n]++; // subtree contains itself
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dist[aj[n][i]] = dist[n]+1; // distance of child = distance of node + 1
            dfs(aj[n][i]); // dfs child
            cnt[n] += cnt[aj[n][i]]; // we add the # of nodes in subtree rooted at child to # of nodes in subtree of parent
        }
    }
}
```
**Example 1:** https://cses.fi/problemset/task/1674
We just use dp to find the size of node subtree using the size of the subtrees of its children. For calculating cnt_of_x, we do 1 + cnt_of_child for every child. Or we can just count nodes in subtree normally: store cnt_of_x = 1 at the beginning, but we print cnt_of_x - 1 in the final answer since we don't include the node x itself.

```cpp
vi aj[200005];
int cnt[200005]; // number of nodes in subtree rooted at n
int vis[200005];
void dfs(int n){
    vis[n] = true;
    // subtree size doesnt contain itself in this problem
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]); // dfs child
            cnt[n] += cnt[aj[n][i]]+1; // we add the # of nodes in subtree rooted at child to # of nodes in subtree of parent
        }
    }
}
 
 
void solve(){
    int n; cin>>n;
    rep(i,2,n+1){
        int u; cin>>u; 
        aj[i].pb(u);
        aj[u].pb(i);
        cnt[i]=0;
        vis[i]=0;
    }
    dfs(1);
    rep(i,1,n+1){
        cout<<cnt[i]<<" ";
    }    
}

```

### Tree Diameter
Calculate maximum length of a path between any 2 nodes in a tree in O(n).
#### DP Approach
First **Root the tree at a random node**. Note that every path of the tree between 2 nodes always contains a "high node", the highest node in the path. We can first fix our highest node x, then the longest path that has x as the highest node is just the sum of the two longest distances from x to a **leaf** in its subtree. 
We need to calculate two function:  
- toleaf(n): the maximum length from node n to a leaf in its subtree
- maxlength(n): the maximum length of path that has n as highest node


toleaf(n) = max(toleaf(child_of_n))+1 
We just pick that max dist from a leaf to children + the node itself.

maxlength(n) = largest toleaf(child_of_n) + second largest toleaf(child_of_n) + 1
We just pick the two largest toleaf(childs) and add their sum, then add the node itself.

```cpp
int toleaf[MAX_N];
int maxlen[MAX_N];
int ans = 0;
void dfs(int n){
	vis[n] = true;
    vector <int> len; len.pb(0); len.pb(0); // if n is a leaf
	rep(i,0,aj[n].size()){
		if(!vis[aj[n][i]]){
			dfs(aj[n][i]);
            len.pb(toleaf[aj[n][i]]); // after dfs we have already calculated toleaf[child]
		}
	}
    sort(len.rbegin(),len.rend());
    toleaf[n] = len[0] + 1;
    maxlen[n] = len[0] + len[1] + 1;
    ans = max(maxlen[n]);
}
```

#### 2 DFS Approach

We choose any node a, dfs and find the furthest node b from that node. Then, we can show that node b is an endpoint of the diameter. We can then dfs b to find the furthest node from that node c. The path from b to c will be the diameter.


### All longest path in O(n)
Calculate for every node the maximum length of a path beginning at that node: If we can find the maximum length starting at every node, the tree diameter problem turns into the max of these lengths.

For every node x, we cam split this into 2 cases: The longest path either goes through a child of x or the parent of x.
For the maximum path length that goes through child of x. Since this path from child x cannot then go back to x, this path is toleaf(x) = max(toleaf(child of x) + 1). We can use dp approach to calculate toleaf(x). 

For the maximum path length that goes through parent of x, we can just calculate max path length of parent and second max path length of parent. This is so if the max length path from parent goes through x itself, we know that this path is impossible since we are constructing x -> parent x -> x, thus we take the second longest path from parent that **does not go back to x**(goes in a different direction). If the first longest path does not pass through x, we take that path.

```cpp

```

### Binary Trees
rooted tree where every node only has 0,1, or 2 children(left and right nodes).  
Ways of traversing a tree:
- pre-order: first process the root, then traverse the left subtree, then traverse the right subtree
- in-order: first traverse the left subtree, then process the root, then traverse the right subtree
- post-order: first traverse the left subtree, then traverse the right subtree, then process the root

We can construct the tree if we know 
- the pre-order and in-order
- the post-order and in-order

**Problem 1:** https://codeforces.com/contest/755/problem/C
If the furthest node reachable by x is y, that means x is in the same tree as y. We can thus create an edge between x and y to denote that they are connected. The number of trees will then be the distinct number of connected components after making the graph.
```cpp
vi aj[200005];
int vis[200005];
void dfs(int n){
    vis[n]=true;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}
 
void solve(){
    int n; cin>>n;
    rep(i,1,n+1){
        int u;cin>>u;
        aj[i].pb(u);
        aj[u].pb(i);
    }
    int cnt =0;
    rep(i,1,n+1){
        if(!vis[i]){
            cnt++;
            dfs(i);
        }
    }
    cout<<cnt<<endl;    
    
}
```
**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=788

Self Editorial:
Since both n and q <= 5000, we can have an O(nq) solution. This allows for brute force. For every query, we can just traverse through the tree starting at starting node. Then directly simulate and update min(curr relevence of path) as we go through.

**What I learned:**
- You can store weights between two edges using adjancency list instead of using a map. Just store a pi $aj[n]$ where pair represents {neighbour, weight}. This way, we can reduce O(log(n)) to O(1).
 
```cpp
vector <pi> aj[5005];
int vis[5005];
int cnt;
int r; // current query relevent
void dfs(int n, int mn){ // {curr node, min curr value on path}
    vis[n]=true;  
    cnt++;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i].first] && min(mn,aj[n][i].second) >= r){ // at least relevence r
            // we visit it     
            dfs(aj[n][i].first,min(mn,aj[n][i].second));        
        }
        
    }
}
void solve(){
    int n,q; cin>>n>>q;
    rep(i,0,n-1){
        int a,b,x;cin>>a>>b>>x;
        aj[a].pb({b,x});
        aj[b].pb({a,x});
    }
    while(q--){
        int a;cin>>r>>a; // relevence, start node
        // reset
        cnt = 0;
        rep(i,1,n+1){
            vis[i] = 0;
        }
        dfs(a,1000000005);
        cout<<cnt-1<<endl; // we dont include start node
    }
    
}
```

**Problem 4:** http://www.usaco.org/index.php?page=viewproblem2&cpid=968
