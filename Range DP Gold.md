### Usage
DP on ranges in an array(l...r): What subarray is the most optimal for some property. Usually:
- Answers for two subarrays (a...b) and (b...c) can be combined to find answer (a...c)
- Given answers for subarray (l...x) an (y...r) for every x and y inside range (l...r), we can calculate the answer of range (l...r) in O(r-l)
**NOTE: usually O(n^3) time complexity, N <= 500.**

We can then get the answer of array with range (l...r) by combining (l...x) and (x+1...r) for every x, then taking the max of these after combining them.
Note that we must calculate subarrays by **smallest to largest length**. There are O(n^2) subarrays, each of them requires O(n) processing by combining two subarrays. Thus overall complexity is O(n^3).
- When given N with small constrains like N <= 500, always try to find a Range DP solution
**Example 1:** https://saco-evaluator.org.za/cms/sapo2015z/tasks/jazz/description
Since N<=500 and we are trying to minimize the deleted characters, we think of range dp by calculating subarrays.
**Problem of Interest:** https://dmoj.ca/problem/ccc16s4
