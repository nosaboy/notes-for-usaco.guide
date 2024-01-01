Direct Acrylic Graph is a graph with directed edges and no cycles.
## Usage
- Ordering the vertices of a directed acyclic graph such that each vertex is visited before its children.
  - For every pair of nodes (u,v) such that u -> v, u comes before v in the sort
  - Sorting is not always unique
- We can perform dp on DAG graphs like Tree


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

```


### BFS: Kahn's Algorithm
**Indegree:** The number of edges point to the node
**Outdegree:** The number of edges going from the node

We note that the first node always has an indegree of 0. Since nothing points to this node, nothing comes before it. Thus, we start with these nodes with indegree 0 and push them into the queue. Then, if we erase all of these nodes from the graph, we get a new DAG with some new nodes whose indegree is 0. Then we repeat the process until we erased and processed everything. 

We can run normal BFS. First we push all starting nodes whose indegree is 0 into the queue and keep track of the indegree of every node. When we process a node, we will first push it into topological sort array, then subtract the indegree of all nodes adjacent to current node by 1(erasing this node). If the indegree of an adjacent node is 0 we will push this into the queue and we run until queue is empty. Based on the nature of BFS, we will always process the node that took 0 nodes to erase first(starting nodes), then 1 node to erase, then 2 nodes to erase, etc.
In the end, if the graph still have edges(going through indegree array, some element is positive), the graph contains a cycle.

```cpp

```
