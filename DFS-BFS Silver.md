### Usage
- Connectivity: can two nodes reach each other? # of connected components?
Run a for loop for each node not visited yet, run dfs through that node and mark all of them visited, increase the number of connected components by 1.
- Finding cycles
   - Directly: Run dfs through some node, it weuriare at a node and we already visited a neighbouring node that is **not its parent**, this means that thre contains a cycle from that node to current node.
   - Check if cycle exists: We count the number of edges & nodes. If the graph is a tree(contains no cycles) it means that there are n nodes and n-1 edges. If there are more than n-1 edges there **must contain a cycle**
- Bipartiteness check: when a graph can be coloured with 2 colours such that every pair of adjacent nodes do not have the same colour
   - Simulate the process using dfs: mark some node blue, then mark all its neighbours red, then search its neighbours and mark their neighbours blue and so on... 
