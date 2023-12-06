#### Calculate # of nodes in subtree or distance from node to root using dp
```cpp
dist[MAX_N] // distance of node to root
cnt[MAX_N] // number of nodes in subtree rooted at n
void dfs(int n){
    vis[n] = true;
    cnt[n]++; // subtree contains itself
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dist[aj[n][i]] = dist[n]+1; // distance of child = distance of node + 1
            dfs(aj[n][i]); // dfs child
            cnt[n] += aj[n][i] // we add the # of nodes in subtree rooted at child to # of nodes in subtree of parent
        }
    }
}
```

### Tree Diameter
Calculate maxmium length of a path between any 2 nodes in a tree in O(n).
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

