
- A connected graph is a graph of vertices such that each vertex can reach every other vertex using undirected edges.
- Spanning tree: set of edges that forms a tree and contains every vertex in the original graph
- Minimum spanning tree: Spanning tree such that the sum of edge weights are minimized
- Using the following algorithms, we can also find the **maximum spanning tree** if process edges in reverse order

**Example 1:** https://cses.fi/problemset/task/1675
Find MST given graph with weighted edges.


### Krushal's Algorithm

Find MST by greedily adding edges. 
Initially, there are no edges on the graph. Then, we go through each edge from **smallest to largest weight.**
Greedy Strategy: For each edge, we always add it if it does not create a cycle, meaning the connected components between the two nodes are different.
This works because if we dont add the minimum edge and instead add some other edge, the solution will always be worse.
At every step, we maintain the connected components of the graph. When all nodes are in the same component, we have found the MST so we break.

**Implementation:**
First sort edges by weight in O(mlogm). Then we go through the edges, each time we see if the two nodes of the edge are in the same connected components. We can do this using DSU in Olog(n). If they are not in the same connected component we add this edge and connect the two nodes. Thus the time complexity is O(mlogm).

```cpp
void solve(){
    int n,m;cin>>n>>m;
    vector <tuple<int,int,int>> edges;
    // reset dsu
    rep(i,1,n+1){
        rdsu[i]=0;
        pdsu[i]=i;
    }
    rep(i,0,m){
        int a,b,w;cin>>a>>b>>w;
        edges.pb({w,a,b});
    }
    sort(edges.begin(),edges.end()); // sort by weight
    ll ans = 0;
    rep(i,0,m){ // process edges  
        int weight = get<0>(edges[i]);
        int a = get<1>(edges[i]);  
        int b = get<2>(edges[i]);  
        if(get(a) == get(b)){ // same connected component
            continue; // we skip
        }
        ans += weight;
        unite(a,b);
    }
    // check if IMPOSSIBLE
    // check if node 1 is in every nodes connected component
    rep(i,2,n+1){
        if(get(1) != get(i)){ // not in same cc
            cout<<"IMPOSSIBLE\n";
            return;
        }
    }
    cout<<ans<<endl;
}   
```


### Prim's Algorithm

Alternative way of finding MST by going through vertices( Dijkstra-like process). 
Start at a random node with no edges added. Our current tree now only contains this node. Each time, we will consider every other node that can be reached by some node in our current tree. We then take the node with the least weight and add it to our tree. We repeat this process until every node is added to our tree.

**Implementation:**
