## Usage
Given a grid, move from the top left corner to the bottom right corner by only moving down or right.
- Compute the most "optimal" path(Ex: max/min value)
- Calculate the number of paths
- Longest common subsequence between two strings

### Most optimal path/number of paths on a grid
Grid DP involves calculating answers for each subrectangle. In this case, calculating dp(x,y) means the number of paths from (1,1) to (x,y).
We can use dp since for every cell (x,y) on the grid, a path can only come from its left (x,y-1) or top (x-1,y).



**Example 1:** Given grid with values, find the path that has the largest sum
We know that for each square on grid, we can only come from the top square(x-1,y) or the left square(x,y-1). Thus the pull DP relationship for (x,y) is $dp[x][y] = max(dp[x][y-1], dp[x-1][y]) + c[x][y]$ were c is the value. 
```cpp
void solve(){
    int n;cin>>n;
    int c[n][n];
    int dp[n][n]; // max path the ends at (i,j)
    rep(i,0,n){
        rep(j,0,n){
            dp[i][j]=0;
            cin>>c[i][j];
        }
    }
    rep(i,0,n){
        rep(j,0,n){
            if(i > 0 && c[i-1][j] == '.'){
                dp[i][j] = max(dp[i][j],dp[i-1][j]); // mx top square
            
            }
            if(j > 0 && c[i][j-1] == '.'){
                dp[i][j] = max(dp[i][j],dp[i][j-1]); // mx left square
            }
            dp[i][j] += c[i][j]; // add current value
        }
    }
    
    cout<<dp[n-1][n-1]<<endl; // print mx path that ends at the end
}
```
**Example 2:** https://cses.fi/problemset/task/1638 
We want to find number of paths with some constrains. The number of paths that reach (x,y) is just the number of paths from (x-1,y) + the number of paths from (x,y-1). So pull dp relationship for (x,y) is $dp[x][y] = dp[x][y-1] + dp[x-1][y]).$ 
```cpp
void solve(){
    int n;cin>>n;
    char c[n][n];
    int dp[n][n]; // number of paths that ends at (i,j)
    rep(i,0,n){
        string s;cin>>s;
        rep(j,0,n){
            dp[i][j]=0;
            c[i][j]=s[j];
        }
    }
    if(c[0][0] == '*' || c[n-1][n-1] == '*'){ // if starting or ending is blocked
        cout<<0<<endl; // we know the answer is 0
        return;
    }
    dp[0][0] = 1; // 1 path from start to start
    rep(i,0,n){
        rep(j,0,n){
            if(i > 0 && c[i-1][j] == '.'){
                dp[i][j] = add(dp[i][j],dp[i-1][j]); // add top square
            
            }
            if(j > 0 && c[i][j-1] == '.'){
                dp[i][j] = add(dp[i][j],dp[i][j-1]); // add left square
            }    
        }
    }
    
    cout<<dp[n-1][n-1]<<endl; // print number of paths the reaches ending
}

```

### Longest Common Subsequence
**Example 3:** https://leetcode.com/problems/longest-common-subsequence/
Find the longest subsequence given two sequences A and B, provided that the subsequence do not have to be consecutive.

We can use grid paths to simulate the "path" of the longest subsequence. Create a 2D grid (n,m). We set the x axis to represent the A and y axis to represent the B. 
Invision two pointers for each subsequence. Here, (i,j) on the grid is the ith element of A and jth element of the B. To construct a subsequence, we can perform some actions:
- Increase i by 1 if i < size(A)
- Increase j by 1 if j < size(B)
- If $A[i] = B[j]$, we can take this common element in our answer for common subsequence. Then, we must increase both i and j by 1 if i < size(A) and j < size(B)

We can simulate these actions on the grid. Increasing i means moving down from a cell. Increasing j means moving right, and increasing i and j means moving down diagonally.
Lets say we are at some cell. Then, we always want to increase our answer. The maximum length sub sequence construction means moving down diagonally as much as possible. Thus, the answer is the path with the most diagonal movements. And we want to always move diagonally from a cell if possible.
To move diagonally, $A[i+1] = B[j+1]$ must hold.
For every cell, the pull DP relation is as follows:
- If $A[i] != B[j],$ $DP[i][j] = max(DP[i-1][j], DP[i][j-1]);$
    - This is because we dont do diagonal since it will only worsen our answer. If diagonal is our best answer, then we can get the same answer by going down then right because the answer wont increase if we go diagonal, so it wont increase if we do it the other way
    - Thus $DP[i-1][j], DP[i][j-1]) \ge DP[i-1][j-1]$ always holds

 - If $A[i] == B[j],$ $DP[i][j] = DP[i-1][j-1]+1;$
     - This will always give us the maximum answer.
     - Note that $DP[i-1][j-1]$ differs from $DP[i-1][j],DP[i][j-1]$ by at most 1 based on construction of paths
     - Thus, we can always get $DP[i-1][j-1]+1 \ge DP[i-1][j],DP[i][j-1]$.
  
**Implementation:**
```cpp
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size(); int m = text2.size();
        int dp[n+1][m+1]; // 1 indexed to account for out of bounds
        memset(dp,0,sizeof(dp));
        for(int i = 1;i<=n;i++){
            for(int j = 1;j<=m;j++){
                if(text1[i-1] == text2[j-1]){ // A[i] = B[j]
                    dp[i][j] = dp[i-1][j-1]+1; // optimal ans
                }
                else{
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1]); // other case
                }
            }
        }
        return dp[n][m]; // final dp value
    }
};
```

**NOTE:** If we add parent(every cell points to the cell it came from), we can **get the longest common subsequence itself** along with its length.

**NOTEx2:** It is possible to solve this using only min(n,m) memory instead of nm memory. However, we can only print the maximum length in this case( see Users Solutions).


