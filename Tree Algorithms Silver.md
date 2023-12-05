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
maxmium length of a path between any 2 nodes in a tree.
#### DP Approach
First **Root the tree at a random node**. Note that every path of the tree between 2 nodes always contains a "high node", the highest node in the path. 
