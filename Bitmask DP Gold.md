## Usage
- Subset states can be stored as integers using bits
- Very small n < 20("smart bruteforce")
- Used to cheese subtasks

```cpp
for(int i = 0; i < (1<<n); i++) {
    // process each subset
}
```
### Optimal selection: 
Given the prices of k products over m days(2d array of integers), we want to buy each product exactly once(k <= n) and buy at most one product a day. What is the minimum total price

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
### Bitmask DP on Permuations
Given elevator with max weight x and n people with known weight, what is minimum number of rides needed if people enter the elevator in optimal order.
- Sometimes we can use bits to calculate most optimal permutation, since n! > 2^n by a lot

Brute force: We iterate over all permuations of n, and for each permutation we simulate the process and add things up: O(n!n)

DP: Let $dp[S]$ be a pair which stores (num of elevators, weight of current elevator). For each subset, we always take the dp that first minimizes num of elevators, and then if num of elevators are the same, then minimize weight of current elevator(default sort pairs). For every subset S, we loop through each element. The pull DP is for each element X, If $weight[X] + dp[SnotincludingX].second > maxweight$, then we are forced to increase num of elevators by 1, so answer is $(dp[SnotincludingX].first+1, weight[X]). Else, we dont have to increase num of elevators, so answer is $(dp[SnotincludingX].first, dp[SnotincludingX].second + weight[X]). The minimum pair over all X will be the answer.
- O(2^n)
```cpp
pi dp[1<<n];
// base case
dp[0] = {1,0}; # one elevator for empty set, 0 weight

rep(i,1,(1<<n)){ // for every subset
  dp[i] = {n+1,0}; // worst case is if it requires n elevators
  rep(j,0,n){
    if(i&(1<<j)){ // if j is an element in set i
      pi osa = dp[(i^(1<<j))]; // most optimal answer without element j
      if(osa.second + weight[j] <= maxweight){ // elevator can still stand this weight in most optimal choice
        osa.second += weight[j]; // add weight of j to elevator
      }
      else{ // it couldnt stand it, must make new elevator
        osa.first++;
        osa.second = weight[j]; // we add the weight to the new elevator
      }
    }
    dp[i] = min(dp[i], osa); // find the most optimal choice(sort by first, then by second)
  }
}

```

### Counting Subsets

Each subset S is assigned an integer value. Calculate for each S the sum of the values of all its subsets. 

Brute force: Go through each pair of subsets, then see if one is a subset of the other and add value: O((2^n)^2)

DP: 

