Direct Acrylic Graph is a graph with directed edges and no cycles.
## Usage
- Ordering the vertices of a directed acyclic graph such that each vertex is visited before its children.
  - For every pair of nodes (u,v) such that u -> v, u comes before v in the sort
  - Sorting is not always unique
- Find cycle in DAG graph
- Perform dp on DAG graphs like Tree
  • how many different paths are there?
  • what is the shortest/longest path?
  • what is the minimum/maximum number of edges in a path?
  • which nodes certainly appear in any path?


## Implementation
**Example 1:** https://cses.fi/problemset/task/1679
Sort the graph topologically or determine its impossible(there is cycle).

### DFS
At any point during DFS, a node can be:
- Already processed
- Currently processing
- Yet to be discovered
In the DFS traversal, we will traverse the graph until we reach an end point. Then, after visiting all its neighbours and dfsing back, we will push our current node into the sorted array and mark it processed.
If current node a has an outgoing edge to node b, then b can be one of the above:
If b was already processed(all its neighbours have been visited and processed), we ignore it.
If b is currently processing, meaning that we havent gone through all its neighbours yet, we note that since b can go back to a through some other path, the graph contains a cycle.
  - We can also think about it this way: if b -> ... -> a, then all nodes after b must come after b in the sorted array. However, a -> b means a comes before b, a contradition.
If b is yet to be discovered we dfs(b).
After everything, we will reverse the sorted array since dfs pushed the deepest(right most) node in first.


```cpp
vi aj[200005];
int vis[200005];
vi sorted; // topologically sorted array
void dfs(int n){
    vis[n] = 1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){ // not yet discovered
            dfs(aj[n][i]); // visit and process it
        }
    }
    // after processing every neighbour node, we push current to the back
    sorted.pb(n); // this way we process all neighbours before current
}
void solve(){
    int n,m; cin>>n>>m;
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); // course a points to course b
    }
    rep(i,1,n+1){
        if(!vis[i]){ // go through every node with indegree 0
            dfs(i);
        }
    }
    reverse(sorted.begin(),sorted.end()); // end up in order
    int idx[n+1];
    rep(i,0,sorted.size()){ // get index for each node inr sorted arr
        idx[sorted[i]] = i;
    }
    // check if there contains a cycle
    rep(i,1,n+1){
        rep(j,0,aj[i].size()){
            if(idx[aj[i][j]] <= idx[i]){ 
            // index of neighbour it points to is in front of than curr node
            // this means there is a cycle
                cout<<"IMPOSSIBLE\n";
                return;

            }
        }
    } 
    rep(i,0,sorted.size()){
        cout<<sorted[i]<<" ";
    }
    cout<<endl;
}   
```


### BFS: Kahn's Algorithm
**Indegree:** The number of edges point to the node
**Outdegree:** The number of edges going from the node

We note that the first node always has an indegree of 0. Since nothing points to this node, nothing comes before it. Thus, we start with these nodes with indegree 0 and push them into the queue. Then, if we erase all of these nodes from the graph, we get a new DAG with some new nodes whose indegree is 0. Then we repeat the process until we erased and processed everything. 

We can run normal BFS. First we push all starting nodes whose indegree is 0 into the queue and keep track of the indegree of every node. When we process a node, we will first push it into topological sort array, then subtract the indegree of all nodes adjacent to current node by 1(erasing this node). If the indegree of an adjacent node is 0 we will push this into the queue and we run until queue is empty. Based on the nature of BFS, we will always process the node that took 0 nodes to erase first(starting nodes), then 1 node to erase, then 2 nodes to erase, etc.
In the end, if the graph still have edges(going through indegree array, some element is positive), the graph contains a cycle. If the final sorted array size != n, the graph contains a cycle.
```cpp
void solve(){
    int n,m; cin>>n>>m;
    vi aj[n+1];
    int vis[n+1];
    int indeg[n+1]={0}; // initally 0
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); // course a points to course b
        indeg[b]++;
    }
    queue <int> q;
    // insert all nodes with indegree 0 first
    rep(i,1,n+1){ 
        if(indeg[i] == 0){
            q.push(i);
        }
    }
    vi sorted; // ans
    while(!q.empty()){
        int x = q.front(); q.pop();
        sorted.pb(x); // process and erase curr node
        rep(i,0,aj[x].size()){
            indeg[aj[x][i]]--; // indegree of neighbour subtract 1
            if(indeg[aj[x][i]] == 0){ // indegree of neighbour = 0
                q.push(aj[x][i]); // process this node next
            }
        }
    }
    // IMPOSSIBLE check: if there are still indegree > 0
    // we havent erased every node
    rep(i,1,n+1){
        if(indeg[i] > 0){
            cout<<"IMPOSSIBLE\n";
            return;
        }
    }
  /* alternatively, if size of final sorted array != n, there is a cycle
    if(sorted.size != n){
      cout<<"IMPOSSIBLE\n";
      return;
    }
  */
    rep(i,0,sorted.size()){
        cout<<sorted[i]<<" ";
    }
    cout<<endl;
    
}   
```

Note that using BFS and a priority queue we can **break ties** in processing and find **lexicographically smallest topological sort**.


### Finding Cycles
**Example 2:** https://cses.fi/problemset/task/1678

We can use DFS topological sorting to find cycle in DAG.
We maintain a stack that store the current path as we traverse using DFS. If we get the second case above(neighbour is a node we are currently processing), we know that this node is currently stored deep down in the stack. We can then pop the stack until we find this node, and store each node in this path in an array. This will be our cycle.

```cpp
vi aj[200005];
int vis[200005];
stack <int> st; // current path
bool instack[200005]; // is current node in stack
vi cycle; // answer
void dfs(int n){
    vis[n] = 1;
    st.push(n); instack[n]=1;
    rep(i,0,aj[n].size()){
        if(cycle.size() == 0 && instack[aj[n][i]]){ // found a cycle
            // we didnt find a cycle yet
            int start = aj[n][i];
            cycle.pb(start);
            while(!st.empty() && st.top() != start){ // backtrack path
                cycle.pb(st.top()); 
                st.pop();
            }
            cycle.pb(start);

        }
        if(!vis[aj[n][i]]){ // not yet discovered
            dfs(aj[n][i]); // visit and process it
        }
        
    }
    instack[n] = 0; // we are finished processing all neighbours
    if(!st.empty()){ // not doing this will cause RTE 
        // while storing cycle we mightve popped more elements from stack
        st.pop(); // we pop current node from stack
    }
    
}
void solve(){
    int n,m; cin>>n>>m;
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); // course a points to course b
    }
    rep(i,1,n+1){
        if(!vis[i]){ // go through every node with indegree 0
            dfs(i);
        }
    }
    if(cycle.size() == 0){ // no nodes in cycle, didnt find cycle
        cout<<"IMPOSSIBLE\n";
        return;
    }
    reverse(cycle.begin(),cycle.end());
    cout<<cycle.size()<<endl;
    rep(i,0,cycle.size()){
        cout<<cycle[i]<<" ";
    }
    cout<<endl;
}   
```


### Dynamic Programming

All DP problems are essentially DP on DAG. A DP state always points to another DP state. Push DP calculates $dp[i+x]$ using $dp[i].$ Thus, we can view this as a directed edge from i to i+x. Then, i points to i+x in the DAG.
After topological sorting, we can perform DP on the graph by going through the sorted array. This way, for every u -> v, $dp[u]$ would be processed before $dp[v]$
Thus, we can calculate longest path, number of paths, shortest path(extended dijkstras), etc. 
**Example 3:** https://cses.fi/problemset/task/1680
Longest Path in DAG using DP

Flatten the graph using topological sort, then for each node push its DP value to all nodes its pointing to.

```cpp
void solve(){
    int n,m; cin>>n>>m;
    vi aj[n+1];
    int vis[n+1];
    int indeg[n+1]={0}; // initally 0
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); // course a points to course b
        indeg[b]++;
    }
    queue <int> q;
    // insert all nodes with indegree 0 first
    rep(i,1,n+1){ 
        if(indeg[i] == 0){
            q.push(i);
        }
    }
    vi sorted; 
    // BFS for topological sort
    while(!q.empty()){
        int x = q.front(); q.pop();
        sorted.pb(x); // process and erase curr node
        rep(i,0,aj[x].size()){
            indeg[aj[x][i]]--; // indegree of neighbour subtract 1
            if(indeg[aj[x][i]] == 0){ // indegree of neighbour = 0
                q.push(aj[x][i]); // process this node next
            }
        }
    }
    int idx[n+1];
    rep(i,0,sorted.size()){ // get index for each node inr sorted arr
        idx[sorted[i]] = i;
    }
    int dp[n+1]; // based on index in sorted array
    rep(i,0,n){
        dp[i] = -1000000005; // set everything to -INF
        // so that there is no chance for any path other than starting from 1
    }
    dp[idx[1]] = 1; // base case, we start at 1
    int p[n+1]; // parent of each node for backtracking paths
    rep(i,0,sorted.size()){ // perform DP
        int node = sorted[i]; // current node
        rep(j,0,aj[node].size()){ // push DP
            // dp[idx[aj[node][j]]] = max(dp[idx[aj[node][j]]], dp[i]+1); // calc max
            if(dp[i]+1 > dp[idx[aj[node][j]]]){
                dp[idx[aj[node][j]]] = dp[i] + 1;
                p[aj[node][j]] = node; // we can backtrack since this is max path
            }
        }
    }
    if(dp[idx[n]] < 0){
        cout<<"IMPOSSIBLE\n";
        return;
    }
    cout<<dp[idx[n]]<<endl; // size
    vi ans;
    // backtrack
    int node = n;
    while(node != 1){
        ans.pb(node);
        node = p[node];
    }
    ans.pb(1);
    reverse(ans.begin(),ans.end()); // we processed back to front so we flip
    rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }
    cout<<endl;
    
}   

```
