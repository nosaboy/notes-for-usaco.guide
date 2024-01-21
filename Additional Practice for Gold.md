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
