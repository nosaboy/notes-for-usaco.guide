**Problem 6:** https://codeforces.com/gym/103886/problem/E
I did exact same as editorial.
This problem was actually quite challenging to approach since I thought it was some complex combo proof. Actually its not bad.
To compute non-palindromes, I had a gut feeling it would be better to compute palindromes and use complementary counting.
We first count total ways to distribute. This is just stars and bars.
For this problem, we should try to calculate specifics then use combinatorics identities to simply sums.
We start by calculating # of ways to distribute specific number n to k slots. We have x-k stars since we must assign 1 to every slot cause a_i is positive. There are k-1 bars so total is $$\binom{n-1}{k-1}$$.
Doing this for every pair of k we get $$\sum_{k=1} \binom{n-1}{k-1} = 2^{n-1}$$ based on combinatorics identity.

We then calculate palindromes. A trick is to not care about second half and focus on first half, since they're gonna be the same anyway. We have floor(n/2) to play with. Then we have n/2 - k stars and k bars. Note that we do the following: lets first put first k slots as the first half of palindrome. Now we want to know if there exists a middle number. If there is, this means first k slots sum < n/2. Thus there is some middle number. Well put whatever we dont use as middle. Then we just do stars and bars with k + 1 slots essentially. And not care about middle. Furthermore, we do floor cause well just put the extra 1 in the middle.
Thus we get $$\binom{n/2}{k}$$ for first k slots(2k slots in total + 1 if theres middle), then total sum for all k is $$\sum_{k=1} \binom{floor(n/2)}{k}.$ However we must add to this the number of ways if theres only 1 middle element, so k = 0. In this case this is just the number n itself, so there is 1 more way.
Thus this has $2^{floor(n/2)}$ ways total based on combo identity.

Thus for every sum $n$ we have $2^{n-1} - 2^{n/2}$. To calculate every n from l to r, we just to prefix sums and gg.

THe key is to break this into something specific and manageable, and simplify using combinatorics identities and etc.

**NOTE:** we can also calculate powers of 2 in logn using binary exponentiation. We just calculate every 2^x where x is a power of 2 in logn(combine together 2^2 * 2^2 = 2^4). Then if we need 2^n we go through binary of n and multiply 2^x if its 1.
```cpp
int pre[100005];
int two[100005];
void solve(){
    int l,r;cin>>l>>r;
    cout<<add(pre[r],-pre[l-1])<<"\n";
}   
 
int main(){
	auto begin = std::chrono::high_resolution_clock::now();
    ios_base::sync_with_stdio(0);
    cin.tie(0);
    // precompute power
    two[0]=1;
    rep(i,1,100005){
        two[i]=mult(two[i-1],2);
    }
    // precompute sums
    pre[0]=0;
    rep(i,1,100005){
        pre[i]=add(pre[i-1],add(two[i-1],-two[i/2]));
    }
    int t;cin>>t;
    
    while(t--){
        solve();
    }
    
	auto end = std::chrono::high_resolution_clock::now();
    auto elapsed = std::chrono::duration_cast<std::chrono::nanoseconds>(end - begin);
   cerr << "Time measured: " << elapsed.count() * 1e-9 << " seconds.\n";
	return 0;
}  
```


**Problem 15:** https://codeforces.com/contest/1114/problem/D
This was actually range DP. I think the reason I was stuck on this problem for a long time was because I didnt really know how to approach. Is it greedy? What is it?
Finally, I found a O(n^2) range DP solution. First, notice that We will always make one move per different cc unless after changing some range, the left and right ranges are coincidentally the same number. Therefore we just eliminated one move since we just checked off 2 cc in one move. Thus we essentially try to find the move that yields the above case the maximum number of times. So the case where we eliminate 2 cc at once happens the max times. To do this, we use range DP. We first compress cc so they are only one number, since numbers in same cc doesnt matter. For every range $dp[l][r]$ we find min moves to make l...r the same. If the two borders are the same, we can just eliminate 2 cc at once. Thus, its just $dp[l+1][r-1]+1$, eliminating the left and right cc, using 1 move. Else, we can just do min(dp[l+1][r]+1, dp[l][r-1]+1), eliminating either left or right cc, using 1 move. 
I used lengths dp instead of left and right since we must calculate all smaller lengths before calculating current length. Then at the end we just print $dp[0][v.size()]$, the whole array.

```cpp
void solve(){
    int n;cin>>n;
    vi v;
    int ar[n];
    rep(i,0,n){
        cin>>ar[i];
    }
    // compress connected components
    v.pb(ar[0]);
    rep(i,1,n){
        if(ar[i] != ar[i-1]){
            v.pb(ar[i]);
        }
    }
    int dp[v.size()][v.size()+1]={0}; // start,length
    memset(dp,0,sizeof(dp)); // everything set to 0, base
    rep(i,2,v.size()+1){
        rep(j,0,v.size()-i+1){
            dp[j][i]=1000000005;
            if(v[j] == v[j+i-1]){ // left right is same
                dp[j][i] = dp[j+1][i-2]+1; // we eliminate both cc
            }
            dp[j][i] = min(dp[j][i], min(dp[j][i-1],dp[j+1][i-1])+1); // we eliminate 1 cc
        }
    }
    cout<<dp[0][v.size()]<<endl;
} 
```
