### Usage
- Subset states can be stored as integers using bits
**Example 1:** Optimal selection: Given the prices of k products over m days(2d array of integers), we want to buy each product exactly once(k <= n) and buy at most one product a day. What is the minimum total price

Brute foce: O(2^(nk)), since we go through every possibility

DP: Let $dp[S][d]$ be the minimum possible cost to buy subset S in d days. We then note that the pull dp for this is you either do nothing on the dth day($=dp[S][d-1]$) or you buy any product in set S on the dth day(If we bought product X on the dth day $=dp[SetSnotincludingX][d-1] + cost[X][d]). Thus, the relationship is $dp[S][d] =min(dp[S][d −1]),min_{x∈S}(dp[S\x][d-1] + cost[x][d]).$
  - We can represent the subset as bits, then a subset can be represented as a number.
  - For every day(n days), we loop through each subset. For each subset(2^k), we loop through each product(k products) and do the dp.
  - This solution is much faster(but not THAT fast) at O(nk2^k)
```cpp
int dp[1<<k][n];
// base case
rep(i,0,n){
  dp[0][i] = 0; // the cost for an empty set is always 0
}
rep(i,0,k){
  dp[1<<i][0] = cost[i][0]; // the min cost of taking one product on the 0th day is just cost of the product on the 0th day

}

rep(i,1,n){ // for each day
  for (int j = 0; j < (1<<k); j++) { // for each subset 
    dp[s][d] = dp[s][d-1]; // dont take anything on ith day
    for (int x = 0; x < k; x++) {
      if (s&(1<<x)) { // takes kth element in subset on ith day
        dp[s][d] = min(dp[s][d], dp[s^(1<<x)][d-1]+cost[x][d]);
      }
    }
  }
}
```

**Example 2:** Given elevator with max weight x and n people with known weight, what is minimum number of rides needed if people enter the elevator in optimal order.
- Sometimes we can use bits to calculate most optimal permutation, since n! > 2^n by a lot

Brute force: We iterate over all permuations of n, and for each permutation we simulate the process and add things up: O(n!n)

DP: 
