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

void dfs(int n){
	vis[n] = true;
	rep(i,0,aj[n].size()){
		if(!vis[aj[n][i]]){
			dfs(aj[n][i]);
		}
	}
}
```
