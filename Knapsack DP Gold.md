Optimize some property by choosing a subset of a set with weights, such that the sum of weights of subset must to exceed some limit.
- Maximize sum of values of a subsets whose weight does not exceed the limit(0/1 Knapsack)
- Find all subsets whose weight does not exceed the limit(given set, how many distinct sums can we form?)
- Find all subsets whose weight is exactly the limit(coin problem)

Problem type 2:
**Given list of weights, determine all numbers that can be constructed using a subset of these weights** in O(nk) where k is # of weights and n is sum of weights(max sum it can form)
https://cses.fi/problemset/task/1633

Let $dp[n][k]$ be true if it is possible to construct sum $n$ using only the first k values. We then have 
- base case: $dp[0][0] = 1$ since there is 1 way to construct a sum of 0 with an empty set.
- inductive step: $dp[n][k] = 1$ if $dp[n-w_k][k-1] = 1$ or $dp[n][k-1] = 1$ since we can only form a sum $n$ with the first k coins if we can form a sum of $n-w_k$ with the first k-1 coins or we can form n with the first k-1 coins originally. We either take the kth coin or dont take the kth coin.

```cpp
int dp[n+1][k+1]={0};
dp[0][0]=1;
rep(j,1,k+1){
  rep(i,1,n+1){
    if(j > 0){
      dp[i][j] |= dp[i][j-1];
    }
    if(i - w[j] >= 0){
      dp[i][j] |= dp[i-w[j]][j-1];
    }
    
  }
}
``` 

We can also only use !D array since we only have to store sum(above we assume that its always k = j-1: the previous dp answers). Let $dp[i]$ be the previous answer that sum up to $i$. We can do nothing, and it will still sum to $i$. We can also construct $i$ using $(i - w_j) + w_j$. Thus the two possibilities to get the new $dp[i]$ become $dp[i] |= dp[i]$ and $dp[i] |=  dp[i-w[j]]$. However, we must go from right to left since calculating $dp[x]$ before $dp[y]$ where x < y may affect the answer of $dp[y]$ since it must be based off of the **previous $dp[x]$ sum** but $dp[x]$ is the current sum. Thus we go largest to smallest in order to prevent wrong calculation.

```cpp
dp[0]=1;
rep(j,1,n+1){
  for(int i = x;i>=1;i--){
    if(i-w[j] >= 0){
      dp[i] |= dp[i-w[j]];
    }       
  }
}
```

**Example 1:** https://cses.fi/problemset/task/1633
These dice rolls are not ordered(sorted smallest to largest), so we can just for every sum = prev + j where j is between 1 to 6, the faces we can get. This is a specific case of the unordered coin problem(CSES Coin Combinations I) but with dices(option is only 1 through 6).
```cpp
void solve(){
    int n;cin>>n;
    int dp[n+1]={0};
    dp[0]=1;
    rep(i,0,n+1){
        rep(j,1,7){
            if(i - j >= 0){
                dp[i] = (dp[i] + dp[i-j])%MOD;
            }
        }

    }
    cout<<dp[n]<<endl;
}
```



**Problem 5:** https://cses.fi/problemset/task/1158
We must only use 1D Knapsack or else it will TLE. This is just coin sums but you have to add each price to the combination that sum up to weight i, then find the max of weight i.


```cpp
void solve(){
    int n,x;cin>>n>>x;
    vi h; vi s;
    h.pb(0); s.pb(0);
    rep(i,0,n){
        int u;cin>>u;h.pb(u);
    }
    rep(i,0,n){
        int u;cin>>u;s.pb(u);
    }
    int dp[x+1];
    memset(dp,0,sizeof(dp));
    
    rep(j,1,n+1){
        for(int i = x;i>=1;i--){
            if(i-h[j] >= 0){
                dp[i] = max(dp[i],dp[i-h[j]]+s[j]);
            }
            
        }
    }
    cout<<dp[x]<<endl;
}
```

**Problem 6:** https://cses.fi/problemset/task/1745/
The coin sums problem using 1D dp array described in example.
```cpp
void solve(){
    int n;cin>>n;
    vi w; 
    w.pb(0); 
    int sum = 0;
    rep(i,0,n){
        int u;cin>>u;w.pb(u);
        sum+=u;
    }
    int dp[sum+1]={0};
    dp[0]=1;
    rep(j,1,n+1){
        for(int i = sum;i>=0;i--){
            if(i - w[j] >= 0){
                dp[i] += dp[i-w[j]];
            }
        }
    }
    vi ans;
    rep(i,1,sum+1){
        if(dp[i]){
            ans.pb(i);
           
        }
    }
    cout<<ans.size()<<endl;
    rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }
}
```

**Problem 7:** https://cses.fi/problemset/task/1093/
We notice that in order for 1...n to be divided into two subsets such that there sum is equal, the sum of each subset must be exactly half of the sum of 1...n. Thus if n*(n+1)/2 is odd it wouldn't work so we print 0. 
We turn this into coin problem but with weights from 1...n and we want to find the number of ways to sum up to sum/2 = (n*(n+1)/2)/2. Then since the sets are ordered pairs the answer must be $dp[sum/2] / 2$.

```cpp
void solve(){
    int n;cin>>n;
    vi w; 
    w.pb(0); 
    int sum = 0;
    rep(i,0,n){
        w.pb(i+1); sum+=i+1;
    }
    if(sum%2==1){
        cout<<0<<endl;
        return;
    }
    int dp[sum+1]={0};
    dp[0]=1;
    rep(j,1,n+1){
        for(int i = sum;i>=0;i--){
            if(i - w[j] >= 0){
                dp[i] = (dp[i] + dp[i-w[j]])%MOD;
            }
        }
    }
    cout<<(dp[sum/2]*inv(2))%MOD<<"\n"; // dp[sum/2]/2 using mod division with inverse
    
}
```
