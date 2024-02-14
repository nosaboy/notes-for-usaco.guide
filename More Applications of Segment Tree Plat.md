### CF EDU: https://codeforces.com/edu/course/2/lesson/4/2
**Problem 1:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/A
Define two operations:
- Update position $i$ to $u$
- Find the maximum subarray sum of the whole array.

For each segment, we can calculate subsegment with maximal sum.
TO do this, we want to calculate for each segment
- The maximum subarray sum in segment
- The maximum prefix sum
- The maximum suffix sum
- The sum of the whole array
Then, the mergining will be either:
the maximum segment sum of left, the maximum segment sum of right, or a subarray intersecting left and right.
The first two case we an just use max subarray sum value. For the third value, it is $suf_{left} + pref_{right}$.
The new pref and suf can either be $prev_{left}$ and $suf_{right}$, but we will also consider if new pref will intersect the two left and right. Thus we have to calc sum of whole array to deal with this case. 
We then recalculate pref,suf,mx as follows:
- mx = $max(mx_{left}, mx_{right}, suf_{left} + pref_{right})$
- pref = $max(pref_{left}, sum_{left}+pref_{right}$
- suf = $max(suf_{right},suf_{left}+sum_{right})
- sum = $sum_{left}+sum_{right}$


**To calculate maximum subarray sum of a fixed segment (l,r), we essentially create a function that does the same as normal segtree range queries, but merge with these operations.**

**It is also possible to get the segment itself(l,r), we must add more values for borders and stuff**


**Problem 2:** https://codeforces.com/edu/course/2/lesson/4/2/practice/contest/273278/problem/B
Define two operations:
- Update position $i$ to $u$, $u$ is 0 or 1
- Find the position of the kth 1 value

tbh we can just use ordered multisets.

### Maximum Subarray Sum Queries
**Example:** https://cses.fi/problemset/task/1190


**Problem of Interest:** https://codeforces.com/problemset/problem/1906/F
We look at every $k$. For every query, we calculate for that point the maximum subarray 
