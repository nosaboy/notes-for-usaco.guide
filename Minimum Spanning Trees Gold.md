
- A connected graph is a graph of vertices such that each vertex can reach every other vertex using undirected edges.
- Spanning tree: set of edges that forms a tree and contains every vertex in the original graph
- Minimum spanning tree: Spanning tree such that the sum of edge weights are minimized
**Example 1:** https://cses.fi/problemset/task/1675
Find MST given graph with weighted edges.


### Krushal's Algorithm

Find MST by greedily adding edges. 
Initially, there are no edges on the graph. Then, we go through each edge from **smallest to largest weight.**
Greedy Strategy: For each edge, we always add it if it does not create a cycle, meaning the connected components between the two nodes are different.
This works because if we dont add the minimum edge and instead add some other edge, the solution will always be worse.
At every step, we maintain the connected components of the graph. When all nodes are in the same component, we have found the MST so we break.

**Implementation:**
First sort edges in O(mlogm)
