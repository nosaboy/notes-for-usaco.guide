## Intro to Tree DP

**Example 1:** https://cses.fi/problemset/task/1130

## Solving For All Roots

**Example 1:** 

### All longest path in O(n): https://cses.fi/problemset/task/1132
Calculate for every node the maximum length of a path beginning at that node: If we can find the maximum length starting at every node, the tree diameter problem turns into the max of these lengths.

For every node x, we cam split this into 2 cases: The longest path either goes through a child of x or the parent of x.
For the maximum path length that goes through child of x. Since this path from child x cannot then go back to x, this path is toleaf(x) = max(toleaf(child of x) + 1). We can use dp approach to calculate toleaf(x). 

For the maximum path length that goes through parent of x, we can just calculate max path length of parent and second max path length of parent. This is so if the max length path from parent goes through x itself, we know that this path is impossible since we are constructing x -> parent of x -> x, thus we take the second longest path from parent that **does not go back to x**(goes in a different direction). If the first longest path does not pass through x, we take that path.

```cpp

```
