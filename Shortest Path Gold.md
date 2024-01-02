# Shortest Path with Unweighted Edges

BFS traverse graph in order of length from origin. Thus, the distance from node 1 to x is the distance at **the first time we visit x**.
We can thus find the shortest distance from 1 to all nodes in O(n+m).

**Example 1:** https://cses.fi/problemset/task/1667/

To keep track of the path, we can just keep track of where each node came from(node with most optimal dist) in array p. After, we start at node n and go back until we reach node 1. For every node we go back to the node it came from.

```cpp
vi aj[100005]; int vis[100005]; int dist[100005];
void solve(){
    int n,m;cin>>n>>m;
    // reset
    rep(i,1,n+1){
        vis[i]=false;aj[i].clear();dist[i]=1000000005;
    }
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    int p[n+1]; // which node it came from

    // bfs shortest path
    dist[1]=0;
    queue<int> q; q.push(1);
    while(!q.empty()){
        int x = q.front(); q.pop();
        rep(i,0,aj[x].size()){
            if(!vis[aj[x][i]]){
                vis[aj[x][i]]=true;
                dist[aj[x][i]] = dist[x]+1; // dist[x] is most optimal due to BFS
                p[aj[x][i]] = x; // set to parent since x is most optimal
                q.push(aj[x][i]);
            }
        }
    }
    if(dist[n] == 1000000005){ // hasnt been reached
        cout<<"IMPOSSIBLE\n";
        return;
    }   
    vi ans;
    int node = n;
    while(node != 1){
        ans.pb(node);
        node = p[node];
    }
    ans.pb(1);
    reverse(ans.begin(),ans.end());
    
    cout<<dist[n]+1<<endl;
	rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }
}   


```

### 0-1 BFS
- Find the shortest path where every edge has weight **0 or 1**

**Implementation:**
We must optimally store and process edges with weights 0 or 1. The BFS idea is to process the smallest current distance in the queue. This way we will always get shortest paths.

Lets store the most optimal dist and second most optimal dist in the **deque**. Then we can account for either 0 or 1 weight. We obvious proess the node with most optimal dist. 
For every edge that has weight 0: We want to push this adjacent node to the **front** of the deque since the dist to this node is still the most optimal current dist.
For every edge that has weight 1: We want to push this node to the **back** of the deque since the dist to this node is 1 more than the most optimal which means its the second most optimal dist.
In order to support both push_front() and push_back() in O(1), **we must use deque**.

**Example 2:** https://oj.uz/problem/view/BOI13_tracks

We know the last animal is the animal at top left corner, and its path is the connected component from top left to bottom right. Then, the other animal must be in the connected component that borders our current animal. If our current is rabbit, fox is the previous animal and it will fill all F connected components that are next to our current connected component.
RRRRR                   FFFFF
RFFFR                   FFFFF
RFRFR        ->         FFRFF
RFFFR                   FFFFF 
RRRRR                   FFFFF


Thus, we can dfs through and just create edges between connected components, then calculate shortest path from the first connected component(at the start) to any other connected component through edges(since colour will be R -> F -> R -> ...)

**However, we can also do this using O-1 BFS**
Let edges between cells with same footprint be 0(they are in the same connected component) and let edges between cells with different footprints be 1(they are in different cc). Then, everytime we reach a different cc by going to a adjacent cell with diff footprint we add 1 to our dist. Similarily if we go to an adjacent cell with the same footprint we are staying in the current cc so we add 0 to our dist.  Thus, the distance from initial cc to current cell's cc is just the shortest path from top left cell to current cell in the O-1 BFS.
The answer will just be the maximum distance cell.

```cpp
int vis[4005][4005]; int dist[4005][4005];
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
void solve(){
    int n,m;cin>>n>>m;
    char v[n][m];
    rep(i,0,n){
        rep(j,0,m){
            cin>>v[i][j];
        }
    }
    int ans = 0;
    // bfs shortest path
    dist[0][0]=1; // start from top left
    deque<pi> q; q.push_front({0,0});
    while(!q.empty()){
        pi a = q.front(); q.pop_front(); // access and pop most optimal element
        int x = a.first; int y = a.second; // current cell
        ans = max(ans,dist[x][y]); // MAX of shortest dist
        rep(i,0,4){
            int r = x+R_CHANGE[i]; int c = y+C_CHANGE[i]; // new coord
            if (r < 0 || r >= n || c < 0 || c >= m || v[r][c] == '.' || vis[r][c]){
                // we do not want to visit
                continue;
            }
            if(!vis[r][c]){
                vis[r][c]=true;
                if(v[x][y] == v[r][c]){ // same cc, weight = 0
                    q.push_front({r,c}); // push to front since weight = 0 more optimal dist than weight = 1
                    dist[r][c] = dist[x][y]; // change distance
                }
                else{ // weight = 1
                    q.push_back({r,c}); // push to back since dist + 1 less optimal than dist + 0
                    dist[r][c] = dist[x][y]+1; // change distance to dist + 1 since weight of edge = 1
                }
                
            }
        }
    }
    cout<<ans<<endl;
}   
```



# Shortest Path with Weighted Edges

### Bellman Ford
Finds shortest path from starting node to every other node in a weighted graph provided that the graph **doesnt have a negative cycle**.
- Use over Dijkstra when there are **negative edges**
#### Implementation
Initially, set distance of start node = 0, and all other nodes distance = INF.
At each instance, we will go through every edge a - b to see if going through this edge will decrease any of the distances a or b. If it does, we will update distance.
Since the number of edges of a shortest path is at most n-1, we will take n-1 rounds. So time is O(nm).
We can speed this up a little bit by breaking if no change occurs for all edge during some round.
```cpp
rep(i,1,n+1){
    dist[i] = 1000000005; // INF
}
dist[1] = 0; // start
rep(i,0,n-1){
    for (auto e : edges) {
	int a = get<0>(e); // node 1
	int b = get<1>(e); // node 2
	int w = get<2>(e); // weight
	// update dist	
	distance[a] = min(distance[a], distance[b]+w);
	distance[b] = min(distance[b], distance[a]+w);
    }

}
```

### Floyd-Warshall
Find distance of every pair of nodes (a,b) of weighted graph in O(n^3).
- Used for querying distance (a,b) given small number of nodes - O(n^3)

We store shortest distance of pairs in 2D array. Then, we go through n rounds. For kth round, the $dist[a][b]$ will contain the shortest path from a to b containing only nodes 1...k.
In the kth round we see how the kth node changes all distance. To do this, k must be in the path. Thus we loop through all pair of nodes (a,b) and see if k is in the shortest path using nodes 1...k:
- If k is in the shortest path of a -> b using only 1...k, then we know that k is an intermediate node in the path. Thus, $dist[a][b] = dist[a][k] + dist[k][b]$ which means that a->k and k->b is the shortest path.
- If k is not in the shortest path(currently shortest path a-> b only uses nodes 1...k-1), $dist[a][b]$ will not change.

After the nth round, we have found shortest path that uses nodes 1...n, thus finding the shortest path for every pair.

**Implementation:**

Use a 2D array to store the most optimal distance of every pair of nodes (a,b). Intially, we set the dist of every node to itself = 0. Then, we set every adjacent edge dist = its weight. Finally we set everything else INF. This is our base case before running algorithm.
```cpp
int dist[n+1][n+1];
for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= n; j++) {
	if (i == j) dist[i][j] = 0; // dist(a,a) = 0
	else if (adj[i][j]) dist[i][j] = adj[i][j]; // there is an edge between 2 nodes
	else dist[i][j] = INF; // else we set to INF
    }
}
```
We go through n round, each round we pick a node that acts as an "intermediate node." We can then go through each pair using nested for loops. Then we can update dist by considering above two choices:
$$dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);$$
Each round will not worsen the answer.
```cpp
for (int k = 1; k <= n; k++) { // for every round we pick node k as middle
    for (int i = 1; i <= n; i++) {
	for (int j = 1; j <= n; j++) {
 	    // for every pair of nodes we see if k is in the path
            // if it was only constructed form nodes 1...k
	    dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j]);
	}
    }
}
```

We can also find the nodes that make up the shortest path. Maintain a 2D array $p[i][j]$ that stores the last node that made up the path. That is, the last k such that $dist[i][j]$ was changed to $dist[i][k] + dist[k][j].$ We can then split this into path i -> k, k, k -> j. Then we determine $p[i][k]$ and $p[k][j]$ and recursively find the path.


**When working with real weights:** there is bound be error which accumulates quickly. Thus we adjust using constant epsilon:
```cpp
if (d[i][k] + d[k][j] < d[i][j] - eps) // adjust comparison
    d[i][j] = d[i][k] + d[k][j]; 
```
**Negative cycles:** If at the end of the algorithm, the distance from a vertex  
$v$  to itself is negative, the graph contains a negative cycle.

We can use the above to **find negative cycles:** https://cp-algorithms.com/graph/finding-negative-cycle-in-graph.html


**Example 1:** https://cses.fi/problemset/task/1672

Query shortest path of any two nodes.

O(n^3) is doable, so we use Floyd-Warshall.

```cpp
void solve(){
    int n,m,q;cin>>n>>m>>q;
    ll adj[n+1][n+1]={0}; //adjacency matrix
    rep(i,1,n+1){
	rep(j,1,n+1){
	    adj[i][j]=1000000000000000005;
        }
    }
    rep(i,0,m){
        ll a,b,w; cin>>a>>b>>w;
        adj[a][b]=min(adj[a][b],w); // account for multiple edges
        adj[b][a]=min(adj[a][b],w);
    }
    // process dist
    ll dist[n+1][n+1];
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (i == j) dist[i][j] = 0; // dist(a,a) = 0
            else if (adj[i][j]!=1000000000000000005) dist[i][j] = adj[i][j]; // there is an edge between 2 nodes
            else dist[i][j] = 1000000000000000005; // else we set to INF
        }
    }
    
    
    for (int k = 1; k <= n; k++) { // for every round we pick node k as middle
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                // for every pair of nodes we see if k is in the path
                // if it was only constructed form nodes 1...k
                dist[i][j] = min(dist[i][j], dist[i][k]+dist[k][j]);
            }
        }
    }
    while(q--){
        int a,b;cin>>a>>b;
        if(dist[a][b]==1000000000000000005){ // = INF
            cout<<-1<<"\n";
        }
        else{
            cout<<dist[a][b]<<"\n";
        }
        
    }
}   
```

### Dijkstra’s Algorithm
Find shortest path from starting node to all other nodes in O(n+mlogn)
- Use over Bellman Ford when all edges are **nonnegative** since its faster

Dijkstra maintains distance of every node and reduces them during search(like Bellman Ford), but only visits each edge exactly once.

**Implementation:**
Initially, we set starting node dist = 0 and every other node dist = INF.
Then, Dijkstra is pretty much BFS on steriods(with weights). At each step, we will visit the node whose current path has the smallest dist. Thus we must maintain a priority queue.
There can be multiple weights of the same node in the pq if there are several ways to go to this node. Thus, the pq allows us to always take the min weight, which will be the final weight of this ndoe. Then for every other instance of this node we'll just skip it. Then, we go through all neighbours and push their distance using current edge weight if unvisited.
This way, the smallest weighted path will always get to a node first.

```cpp
rep(i,1,n+1){
	distance[i] = INF;
	vis[i] = 0;
}
distance[x] = 0;
// pq that sorts smallest weight to largest
priority_queue<pair<long long, int>, vector<pair<long long, int>>, greater<pair<long long, int>>> pq;

int start = 0;
dist[start] = 0;  // The shortest path from a node to itself is 0
pq.push({0, start}); // 0 weight
while (!pq.empty()) {
        pair<ll,int> x = pq.top(); // {curr min dist of node, curr node}
        ll cdist = x.first; // 
        int node = x.second; // cur node
        pq.pop();
        if (vis[node]) { continue; } // we already processed node
        vis[node]=1; // visit node(otherwise TLE)
        rep(i,0,aj[node].size()){
		pi y = aj[node][i]; // {node, weight}
		// If we can reach a neighbouring node faster, we update its min dist
		if (cdist + y.second < dist[y.first]) {
			pq.push({dist[y.first] = cdist + y.second, y.first}); // push neighbour into pq
		}
        }
}

```

**Example 2:** https://cses.fi/problemset/task/1671

Print shortest path from node 1 to every other node

WARNING: I TLEd quite a bit because I didnt put vis = 1 after( so "continue" didnt work).
Putting continue allows us to only process each node once. 
```cpp
void solve(){
    int n,m;cin>>n>>m;
    vector <pi> aj[n+1];
    ll dist[n+1]; int vis[n+1];
    rep(i,0,m){
        ll a,b,w; cin>>a>>b>>w;
        aj[a].pb({b,w});
    }
    rep(i,1,n+1){
        dist[i] = 1000000000000000005;
        vis[i] = 0;
    }
    // pq that sorts smallest weight to largest
    priority_queue<pair<ll, int>, vector<pair<ll, int>>, greater<pair<ll, int>>> pq;

    int start = 1;
    dist[start] = 0;  // The shortest path from a node to itself is 0
    pq.push({0, start}); // 0 weight
    while (!pq.empty()) {
        pair<ll,int> x = pq.top(); // {curr min dist of node, curr node}
        ll cdist = x.first; // 
        int node = x.second; // cur node
        pq.pop();
        if (vis[node]) { continue; } // we already processed node
        vis[node]=1; // visit node(otherwise TLE)
        rep(i,0,aj[node].size()){
            pi y = aj[node][i]; // {node, weight}
            // If we can reach a neighbouring node faster, we update its min dist
            if (cdist + y.second < dist[y.first]) {
                pq.push({dist[y.first] = cdist + y.second, y.first}); // push neighbour into pq
            }
        }
    }
    rep(i,1,n+1){
        cout<<dist[i]<<" ";
    }
    cout<<"\n";
}   

```
