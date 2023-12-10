### Usage
DP on ranges in an array(l...r): What subarray is the most optimal for some property. Usually:
- Answers for two subarrays (a...b) and (b...c) can be combined to find answer (a...c)
- Given answers for subarray (l...x) an (y...r) for every x and y inside range (l...r), we can calculate the answer of range (l...r) in O(r-l)
**NOTE: usually O(n^3) time complexity, N <= 500.**

We can then get the answer of array with range (l...r) by combining (l...x) and (x+1...r) for every x, then taking the max of these after combining them.
Note that we must calculate subarrays by **smallest to largest length**. There are O(n^2) subarrays, each of them requires O(n) processing by combining two subarrays. Thus overall complexity is O(n^3).
- When given N with small constrains like N <= 500, always try to find a Range DP solution
**Example 1:** https://saco-evaluator.org.za/cms/sapo2015z/tasks/jazz/description
Since N<=500 and we are trying to minimize the deleted characters, we think of range dp by calculating subarrays. Lets assume that all previous ranges whose length is smaller than the current range has been calculated. Now we want to calculate this range. We can do this in two ways. We note that if the two characters at the end match, a possible answer is just $dp[l+1...r-1] + 0$ since we just match the two characters. Else, we can still do $dp[l+1...r-1] + 2$ since we create a character for each character at the end.
We can also merge two ranges $dp[l][l+x] + dp[l+x+1][r]$ together. We then find the minimum for this range by iterating through these two ranges + the first case.
**editorial did it slightly differently, matching inside instead of outside.**
```cpp
void solve(){
    string s;cin>>s;
    int n = s.size();
    int dp[n+2][n+2]={0};
    // precompute length 1 and 2
    rep(i,0,n){
        dp[i][i]=1;
    }
    rep(i,0,n-1){
        if(s[i] == s[i+1]){
            dp[i][i+1] = 0;
        }
        else{
            dp[i][i+1] = 2;
        }
    }

    rep(i,3,n+1){ // length 3 to n
        rep(j,0,n-i+1){ // left
            int l = j; int r = j + i -1;
            if(s[l] == s[r]){ // we can match outer characters
                dp[l][r] = dp[l+1][r-1];
            }
            else{ // we cant match, so it takes 2 operations
                dp[l][r] = dp[l+1][r-1]+2;
            }
            rep(x,0,i-1){
                // merge two subranges together
                dp[l][r] = min(dp[l][r], dp[l][l+x] + dp[l+x+1][r]);
            }
        }
    }
    cout<<dp[0][n-1]<<endl;
    
}
```

**Problem of Interest:** https://dmoj.ca/problem/ccc16s4
