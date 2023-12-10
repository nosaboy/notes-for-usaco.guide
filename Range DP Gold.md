### Usage
DP on ranges in an array(l...r): What subarray is the most optimal for some property. Usually:
- Answers for two subarrays (a...b) and (b...c) can be combined to find answer (a...c)
- Given answers for subarray (l...x) an (y...r) for every x and y inside range (l...r), we can calculate the answer of range (l...r) in O(r-l)
**NOTE: usually O(n^3) time complexity, N <= 500.**


**Problem of Interest:** https://dmoj.ca/problem/ccc16s4
