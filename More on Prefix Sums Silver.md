### Maximum Subarray Sums
Find the maximum sum over all subarrays(there may be negative values) 
- O(n) precomputing
- O(n) processing
#### Example 1 
https://cses.fi/problemset/task/1643
Sum of subarray $[l,r] = pre[r] - pre[l-1].$ The maximum subarray sum that ends with r is $pre[r] - pre[l-1]$ where $pre[l-1]$ is minimized. We keep track of the minimum left $pre[l-1]$ and iterate through each $r$ and find the max of $pre[r] - mn.$
```cpp
void solve(){
    int n;
    cin>>n;
    ll pre[n+1];
    pre[0] = 0;
    rep(i,0,n){
        int u;
        cin>>u;
        pre[i+1] = pre[i] + u;

    }
    ll mn = 0; //minimum is set to pre[0] = 0 at the start
    ll mx = -1000000005;
    rep(i,1,n+1){
        mx = max(mx, pre[i] - mn);
        mn = min(mn, pre[i]);
    }
    cout<<mx<<endl;
}
```

### 2D Prefix Sums
Find the sum of all values in box with left top corner (i,j) and bottom right corner (x,y)
- O(nm) precomputing
- O(1) query
Precomputing: Let's first calculate sum of all values in box with top left corner at **(0,0)** and bottom right corner at (x,y). We take a dp approach, where we are at (x,y) after we calculate the sum for (x-1,y), (x,y-1), and (x-1,y-1). We then have $dp[x][y] = dp[x-1][y] + dp[x][y-1] - dp[x-1][y-1] + arr[x][y].$ This also takes a PIE approach where we first count $dp[x-1][y-1]$ twice then subtract that to count everything exactly once.

Query: We also take a similar PIE approach. First we subtract sum with bottom right at (i-1,y) and (x,j-1) from (x,y). This over subtracted (i-1,j-1) once. Thus, we add (i-1,j-1) once. So we have $dp[x][y] - (dp[i-1][y] + dp[x][j-1] - dp[i-1][j-1]).$
#### Example 1
https://cses.fi/problemset/task/1652
We add 1 if there is a tree and 0 if it doesn't. We also need to add 1 to the size of prefix sum similar to 1D prefix sum to account for pre[0][] = 0
```cpp
void solve(){
    int n,q;
    cin>>n>>q;
    int arr[n+1][n+1];
    rep(i,1,n+1){
        rep(j,1,n+1){
            char c; cin>>c;
            if(c == '*'){
                arr[i][j]=1;
            }
            else{
                arr[i][j]=0;
            }
        }
    }
    //preprocessing
    int pre[n+1][n+1]={0};
    rep(i,1,n+1){
        rep(j,1,n+1){
            pre[i][j] = pre[i][j-1] + pre[i-1][j] - pre[i-1][j-1] + arr[i][j];
        }
    }
    while(q--){
        int i,j,x,y; cin>>i>>j>>x>>y;
        cout<<pre[x][y] - pre[i-1][y] - pre[x][j-1] + pre[i-1][j-1]<<endl;
    }

}
```
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=919
Note: This is very hard to explain without a diagram.
This is the same as the 1D prefix sum problem where you have to fill a range of values and you just put $pre[l]++;$ and $pre[r+1]--;$ then run prefix sum so that all values in the range $[l,r]$ gets + 1. We use a similar strategy but in 2D. Lets say we want to add 1 to rectangle with top left at (i,j) and bottom right at (x,y). We first fix the top left value by adding 1 to (i,j) so that after taking the prefix sum of everything, all values to the right bottom of (i,j) will have 1: (a,b) such that i <= a and j <= b. We do not want any value that is outside of the right bound (x.y), so we subtract 1 from each of pre[x+1][j] and pre[i][y+1]. When we take the prefix sum now, all values will now go back to 0 except for all values to the right down of (x,y) since we subtracted it twice, so we must add back once. So to add 1 to every value in rectangle (i,j) to (x,y), we need to do: $pre[i][j]++, pre[x+1][j]--,pre[i][y+1]--, pre[x+1][y+1]++$.
```cpp
void solve(){
    ll n,k; cin>>n>>k;
    ll pre[1002][1002]={0};
    rep(i,0,1001){
        rep(j,0,1001){
            pre[i][j] = 0;
        }
    }
    //constructing prefix sums
    rep(i,0,n){
        ll a,b,x,y;
        cin>>a>>b>>x>>y; x--; y--; // make sure we minus 1 here since the top right is in coordinates and not the grid itself
        // for example, (1,1) (5,5) covers from all grids that have x,y = 1 to 4: 16 area instead of 25 area

        a++; b++; x++; y++; // we wanna leave 0 blank since pre[0][] = 0, so we shift all coordinates by 1
        pre[a][b]++;
        pre[a][y+1]--;
        pre[x+1][b]--;
        pre[x+1][y+1]++;

    }
    //precomputation (adding values together)
    rep(i,1,1002){
        rep(j,1,1002){
            pre[i][j] += pre[i][j-1] + pre[i-1][j] - pre[i-1][j-1];
        }
    }
    // count values with exactly k
    ll ans = 0;
    rep(i,1,1002){
        rep(j,1,1002){
            if(pre[i][j] == k){
                ans++;
            }
        }

    }
    cout<<ans<<endl;
}

```


**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=416
We want to brute force the problem where we check the number of grass for every (x,y). The range of which Bessie can travel with k steps form a diamond square with center (x,y) and 4 corners at (x-k,y), (x+k,y), (x,y-k), (x,y+k). We can use a similar "prefix sum/dp" approach but instead of rectangles we have triangles. Let $pre[i][j]$ be the sum of all values in an upside down right isosceles triangle with the point(right angle) at (i,j) where the base is the very top. Its like all the values above (i,j) inclosed by y = x and y = -x. To compute the prefix at (x,y), we assume we know This means we can first find the prefix of the bottom corner of our diamond square at (x,y+k), then we want to subtract the triangles on either side with corners (x-k,y), (x+k,y) to eliminate the values on the side. However, we have eliminated the triangle with corner (x,y-k) twice, so we have to add it back once to get the value of the diamond square. So, the values with center (x,y) is $pre[x][y+k] - pre[x-k][y] - pre[x+k][y] + pre[x][y-k]$. We then iterate through all (x,y) and take the maximum.
**HAVENT SOLVED YET***
```cpp

void solve(){
    ll n,k; cin>>n>>k;
    ll pre[1505][1505]={0};
    ll arr[1505][1505]={0};
    ll left[1505][1505]={0};
    ll right[1505][1505]={0};
    rep(i,0,1505){
        rep(j,0,1505){
            pre[i][j] = 0;
        }
    }
    
    //constructing prefix sums
    rep(i,0,n){
        rep(j,0,n){
            cin>>arr[i+1000][j+1000]; //shift by 400 to prevent out of bounds
        }
    }
    
    //precomputation (adding values together)
    rep(i,2,1500){
        rep(j,2,1500){
            pre[i][j] = pre[i-1][j-1] + pre[i-1][j+1] - pre[i-2][j] + arr[i-1][j] + arr[i][j];
        }
    }
    //cout<<pre[1000][1000]<<endl;
    // left and right line y = x and y = -x
    rep(i,1,1500){
        rep(j,1,1500){
            left[i][j] = arr[i][j] + left[i-1][j-1];
            right[i][j] = arr[i][j] + right[i-1][j+1];
        }
    }
    // count values with exacty k
    ll ans = 0;
    rep(i,1000,1000+n){
        rep(j,1000,1000+n){
            //cout<<i<<" "<<j<<" "<<pre[i+k][j]<<" "<<pre[i+k][j] + left[i-1][j-k-1] + right[i-1][j+k+1] - pre[i-1][j-k] + pre[i-1][j+k] + pre[i-k-1][j]<<endl;
            ans = max(ans, pre[i+k][j] - left[i-1][j-k-1] - right[i-1][j+k+1] - pre[i-1][j-k] - pre[i-1][j+k] + pre[i-k-1][j]);
        }
    }
    
    cout<<ans<<endl;
}
```
