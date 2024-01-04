## Usage
- Find LIS in O(nlogn)
- Longest non-decreasing subsequence
    - LIS except we can use same numbers in a sequence
    - Just modify the inequalities of code for LIS
- Number of longest increasing subsequences    
**Example 1:** https://cses.fi/problemset/task/1145
Given an array of integers, calculate longest increasing subsequence.

O(n^2) solution: For every i we calculate LIS that ends at position i. The LIS that ends at i is 
$dp[i] = max(dp[j])+1$ if $j < i$ and $a[j] < a[i].$ In other words, we are ending at position i at value $a[i]$ so we want the previous $a[j]$ that is smaller than $a[i]$ and has the longest subsequence already. Then we just add $a[i]$ to the end of the sequence.

Remember, we must first set every dp value to 1 since the smallest LIS ending at i is just $a[i]$ itself.

**NOTE:** We can also restore the sequence by implementing parent array $p[n]$ which points to previous element that gave the max $dp[j]$ for valid $j$.

## Fast Solution to Calculation LIS in O(nlogn)
### Smart Way
Let $L[i]$ be an array for each i. Let $L[i][j]$ be the smallest element at position l such that l < i and there is a subsequence ending at position l with length j+1. In other words we are storing smallest element before i that is an ending of a increasing subsequence length 1,2,3,...
We note that $L_i$ is strictly increasing. We know that if j is valid, then theres some sequence $a_{b_0}, a_{1}, \cdots, a_{b_l}$ that is increasing subsequence ending at b_l with length j+1. We then note that $a_{b_0}, a_{1}, \cdots, a{b_{l-1}}$ is a subsequence length j. This means the previous j must be <= $a{b_{l-1}}$. So $a{b_{l-1}}$ < a_{b_l}$ means $L[i]]j-1] < L[i][j]$ since $L[i]]j-1] < a{b_{l-1}}$ < a_{b_l} = L[i][j]$.

To find the LIS that ends at each i, we just take the biggest $L[i][j]$ such that $L[i][j] < a[i].$ This is because we want to maximum length possible so we want the maximum j. The maximum j possible such that $a[i]$ is the last element must satisfy the above. Thus, we can just binary search(use a sorted set) to find maximum j since we know that **$L[i]$ is strictly increasing**.

For each i transition, we only need to change at most 1 element of $L[i]$ to get $L[i+1]$. This is because it is only optimal to add $a[i]$ to the end of $L[i][j]$. If we add $a[i]$ to anything smaller it isnt optimal cause $L[i][l+1] < a[i]$. However, we know that $L[i][j+1] >= a[i]$. Thus, it is always optimal to add to $a[i]$ to the back of $L[i][j]$ and change to $L[i][j] = a[i]$. In the case that $L[i][j+1]$ is a new value, it means that we have never gotten a subsequence length j+1 before. Thus we push_back a new value in the set.

```cpp
void solve(){
    int n;cin>>n;
    vi a;
    rep(i,0,n){
        int u;cin>>u;a.pb(u);
    }
    vi dp; // store current L values: min ending element of LIS length j
    rep(i,0,n){
        int pos = lower_bound(dp.begin(), dp.end(), a[i]) - dp.begin();
        // position of the j+1 such that L[j+1] >= a[i]
        if(pos == dp.size()){ // j+1 not in array
            dp.pb(a[i]); // first time j+1 length, make new value in L
        }
        else{
            dp[pos] = a[i]; // we just set curr L[j+1] = a[i]
        }
    }
    cout<<dp.size()<<endl; // longest length is just biggest j(size)
}   

```


### Better brute force way(using RMQ)
- Easier to understand and derive on the spot
- Slower constant factor than smart way

Consider brute force solution. We want all j before it such that $a[j] < a[i]$. We can achieve this using RMQ updates and query. We store the max dp of every $a[i]$ in the segment tree. Looping through i, for each $a[i]$ we get the max element $mx$ from 1 to $a[i]-1$ since these numbers are all smaller. Then, we update the value of $a[i]$ in the segment tree with $max(prev, mx+1).$

```cpp

```

#### Number of possible LIS
This task can only be achieved using RMQ.
Using RMQ approach, we store segtree in pair {length, the number of LIS ending at i}. The transition from j to i is then {mx+1, the sum of all $pair[j].second$ whose $pair[j].first = mx$}.
In other words, for each i we gte the number of ways by summing up all ways for all $j$ that have the mx value.
```cpp

```



**Example 2:** https://cses.fi/105/submit/C
We essentially want to find the minimum set of circuts such that for each set, all wires do not intersect. 
We note that two wires i and j where $l_i < l_j$do not intersect if $r_i < r_j$ as well. For each set, we want this property to be satisfied for all i and j. Thus we want to find the minimum number of increasing subsequences for pairs to cover all wires. 


This means we want to 
