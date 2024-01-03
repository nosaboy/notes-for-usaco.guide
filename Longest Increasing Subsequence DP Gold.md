**Example 1:** https://cses.fi/problemset/task/1145
Given an array of integers, calculate longest increasing subsequence.

O(n^2) solution: For every i we calculate LIS that ends at position i. The LIS that ends at i is 
$dp[i] = max(dp[j])+1$ if $j < i$ and $a[j] < a[i].$ In other words, we are ending at position i at value $a[i]$ so we want the previous $a[j]$ that is smaller than $a[i]$ and has the longest subsequence already. Then we just add $a[i]$ to the end of the sequence.

**NOTE:** We can also restore the sequence by implementing parent array(pointing to previous element that had max dp)

## Fast Solution to Calculation LIS in O(nlogn)
### Smart Way
Let $L[i]$ be an array for each i. Let $L[i][j]$ be the smallest element at position l such that l < i and there is a subsequence ending at position l with length j+1. In other words we are storing smallest element before i that is an ending of a increasing subsequence length 1,2,3,...
We note that $L_i$ is strictly increasing. We know that if j is valid, then theres some sequence $a_{b_0}, a_{1}, \cdots, a_{b_l}$ that is increasing subsequence ending at b_l with length j+1. We then note that $a_{b_0}, a_{1}, \cdots, a{b_{l-1}}$ is a subsequence length j. This means the previous j must be <= $a{b_{l-1}}$. So $a{b_{l-1}}$ < a_{b_l}$ means $L[i]]j-1] < L[i][j]$ since $L[i]]j-1] < a{b_{l-1}}$ < a_{b_l} = L[i][j]$.

To find the LIS that ends at each i, we just take the biggest $L[i][j]$ such that $L[i][j] < a[i].$ This is because we want to maximum length possible so we want the maximum j. The maximum j possible such that $a[i]$ is the last element must satisfy the above. Thus, we can just binary search(use a sorted set) to find maximum j since we know that **$L[i]$ is strictly increasing**.

For each i transition, we only need to change at most 1 element of $L[i]$ to get $L[i+1]$. This is because it is only optimal to add $a[i]$ to the end of $L[i][j]$. If we add $a[i]$ to anything smaller it isnt optimal cause $L[i][l+1] < a[i]$. However, we know that $L[i][j+1] >= a[i]$. Thus, it is always optimal to add to $a[i]$ to the back of $L[i][j]$ and change to $L[i][j] = a[i]$. In the case that $L[i][j+1]$ is a new value, it means that we have never gotten a subsequence length j+1 before. Thus we push_back a new value in the set.

```cpp


```


### Better brute force way(using RMQ)
