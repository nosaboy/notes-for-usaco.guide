## Usage
- Query on subtrees
  - Just Segtree but on trees
 
- 

### How it works
We visit the tree starting form the root using dfs. We then store the **order** of nodes we've visited in a 1D array. Thus, any subtree will be a continous segment on the array since we are visiting all children of a node before dfsing back. 
The idea is to preprocess a rooted tree such that every subtree corresponds to a contiguous range on an array. We are pretty much forcing subtrees to be together so we can perform **range query updates** on subtrees.
#### Subtree Queries
We can thus **query associative operations on subtrees** using a segtree data structure, since we now have the 1D array.
We must first store the order for every node, then the size of that node. This is because we note that after visiting this node, every node below it will be visited next until we dfs back. Thus, the range of subtree x on the ordering array is just position_x to position_{x+size of subtree x}. 
**Example 1:** https://cses.fi/problemset/task/1137
Update and query subtree sums
**Implementation:**
We want to maintain 3 arrays: 
- Ordering of each node(what is its position on the 1d array/segtree)
- Subtree size(for interval length)
- Value of each node(value in the segtree for each node/index)

To get ordering, we just dfs through the tree normally and keep the ordering vector. For each unvisited node, we push it into the vector, then visit its children.
To get subtree size, we just do dp.
To update value, we get position of node in the segtree, then update the segtree.
To get sum of interval, we first get position of node $i$ and size of nodes subtree $x$, then query segment tree for $sum[i...i+x).$
Thus, we can update and query subtrees in O(logn).
#### Path Queries
**Problem 2:** https://cses.fi/problemset/task/1138
We want to calculate assoiative operations on paths instead of subtrees.
We can perform update node value and calculate path sum both in O(logn)
