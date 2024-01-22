**Problem 3:** https://codeforces.com/problemset/problem/1525/C
Tim: 1 - 2 hours
Hint 1(kinda knew this alr): Odds and Even positions never collide, consider odd and even seperately, which they will always collide.

OKOK, we then do by greedy. Note we always want RL to pair with each other before we pair up LL or RR. This is because RL will alwasy explode sooner.

Bruh the impl is kinda bad but ya gg. This problem I did **O(n)** since I thought logic would be way easier. **I still dont know how you solve this with sets in O(nlogn) pls review editorial.**
Basically we know that the closest RL combinations will always explode first before LL or RR combinations. Then RRLL will be pairs like (1,4) and (2,3) since (2,3) explodes first. We use stack to simulate and explode this. Then we add all LL and RR left.
Then, we know that LL closest to wall 0 will always explode first since closest L will explode with second closest L. We thus just explode pairs of LL. RR explosions follow similar logic where we always explode closest R to m.
Then, at the end of explosion if there is 1 L or R left, we can do -1. If both size is odd( so one L and R is left and not paired up and exploded), we can pairs and explode these two.

Impl kinda brutal tho no cap.
- The main takeaway is that when everything is happening and looks messy, we try to break down into cases & steps and solve
```cpp
int ans[300005]={0};
int n,m;
void osa(vector<tuple<int,int,int>> &a){
    tuple<int,int,int> endleft; // position of element if size odd
    tuple<int,int,int> endright; // position of last element size odd
    vector <tuple<int,int,int>> left;
    vector <tuple<int,int,int>> right;
    stack <tuple<int,int,int>> st; // montonic stack to simulate RL elimination
    rep(i,0,a.size()){
        
        if(get<1>(a[i]) == 'R'){
            st.push(a[i]);
        }
        else{
            if(st.size()){
                tuple<int,int,int> ok = st.top(); st.pop();
                ans[get<2>(a[i])]=(get<0>(a[i])-get<0>(ok))/2;
                ans[get<2>(ok)]=(get<0>(a[i])-get<0>(ok))/2;
            }
        }
    }
    rep(i,0,a.size()){
        if(ans[get<2>(a[i])]==0){
 
            if(get<1>(a[i]) == 'L'){
                left.pb(a[i]);
            }
            else{
                right.pb(a[i]);
            }
        }
    }
    if(left.size()%2){
        endleft = left[left.size()-1];
    }
    if(right.size()%2){
        endright = right[0];
    }
    for(int i = 1;i<left.size();i+=2){ // merge lefts two at a time
        ans[get<2>(left[i])] = (get<0>(left[i]) + get<0>(left[i-1]))/2;
        ans[get<2>(left[i-1])] = (get<0>(left[i]) + get<0>(left[i-1]))/2;
    }
    for(int i = right.size()-2;i>=0;i-=2){ // merge rights 
        ans[get<2>(right[i])] = (2*m-(get<0>(right[i]) + get<0>(right[i+1])))/2;
        ans[get<2>(right[i+1])] = (2*m-(get<0>(right[i]) + get<0>(right[i+1])))/2;
    }
 
    if(left.size()%2 && right.size()%2){ // both has 1 left over
        ans[get<2>(endleft)] = (get<0>(endleft) + (m-get<0>(endright)) + m)/2;
        ans[get<2>(endright)] = (get<0>(endleft) + (m-get<0>(endright)) + m)/2;
    }
    else if(left.size()%2){ // only left has 1 left over
        ans[get<2>(endleft)] = -1; // cant collide
    }
    else if(right.size()%2){ // only right has 1 left over
        ans[get<2>(endright)] = -1;
    }
    
}
void solve(){
    cin>>n>>m;
    vi v;
    char c[n];
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    memset(ans,0,sizeof(ans));
    vector <tuple<int,int,int>> a;
    vector <tuple<int,int,int>> b;
    multiset <int> ms;
    rep(i,0,n){
        cin>>c[i];
        if(v[i]%2){ // odd position
            a.pb({v[i],c[i],i});
        }
        else{
            b.pb({v[i],c[i],i});
        }
    }
    sort(a.begin(),a.end());
    sort(b.begin(),b.end());
    // odd 
    osa(a);
    // even
    osa(b);
    rep(i,0,n){
        cout<<ans[i]<<" ";
    }
    cout<<endl;
   
}  
```
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
**Problem 12:** https://codeforces.com/contest/835/problem/D
Pretty much a k palindrome is just one such that if we divide it by half k times, the left and right will always be palindromes.

First thought: I genuinly think we do range DP on this too or some, cause we can easily combine palindromes. Ya this is literally free instasolve.
We can just see for every rage l...r, whatever $DP[firsthalf], DP[secondhalf]$ is, where DP stores maximum k such that l...r is a k-palindrome. If string firsthalf == secondhalf, we can convert l...r to a $min(DP[firsthalf], DP[secondhalf])+1$ palindrome. To compare strings we just use hashing. Else $DP[l...r]=0$ since its not a palindrome. Overall free idk since n = 5000 so O(n^2) is possible.

**NOTE:** Using int DP will MLE. We can **cheese memory using short type** since short only needs 2 bytes but can only hold up to like 30000 or smth. We only need this much case k can be at most 20 cause as k increases by 1 length increases by 2x. 

```cpp
const ll M = (1LL << 61) - 1; // large prime for mod
const ll P = uniform_int_distribution<ll>(0, M - 1)(RNG); // random base
ll hsh[5005]={0};
ll revhsh[5005]={0};
vector <ll> pw = {1LL};
__int128 mul(ll a, ll b) { return (__int128)a * b; }
ll mod_mul(ll a, ll b) { return mul(a, b) % M; }
void calchash(string s){
    while (pw.size() < s.size()) {
        pw.push_back(mod_mul(pw.back(), P) % M);
    }
    hsh[0] = 0;
		for (int i = 0; i < s.size(); i++) {
			hsh[i + 1] = (mul(hsh[i], P) + s[i]) % M;
		}
    reverse(s.begin(),s.end());
    revhsh[0] = 0;
		for (int i = 0; i < s.size(); i++) {
			revhsh[i + 1] = (mul(revhsh[i], P) + s[i]) % M;
		}
}
long long gethash(int start, int end) {
	ll raw_val =
		    hsh[end + 1] - mod_mul(hsh[start], pw[end - start + 1]);
		return (raw_val + M) % M;
}
long long getrevhash(int start, int end, int n) { // hash reverse string
    end = n-end;
    start = n-start;
	ll raw_val =
		    revhsh[start + 1] - mod_mul(revhsh[end], pw[start - end + 1]);
		return (raw_val + M) % M;
}
void solve(){
    
    string s;cin>>s;
    int n = int(s.size());
    calchash(s);
    short dp[5005][5005]={0};
    int osa[n+1]={0};
    rep(i,0,n){ // base case
        dp[i][i]=1;
        osa[1]++;
    }
    rep(i,2,n+1){ // length
        rep(j,0,n-i+1){ // left
            int len = i/2;
            if(gethash(j,j+len-1) == getrevhash(j+i-len,j+i-1,n-1)){ // first half == second half
                dp[j][j+i-1] = min(dp[j][j+len-1], dp[j+i-len][j+i-1])+1; // min(firsthalf,sechalf)+1
            }
            
            osa[dp[j][j+i-1]]++;
        }
        
    }
// calc prefix sum cause every k-palindrome is also a k-1 palindrome, k-2 palindrome, etc.
    for(int i = n-1;i>=1;i--){
        osa[i]+=osa[i+1];
    }
    rep(i,1,n+1){
        cout<<osa[i]<<" ";
    }
    cout<<endl;
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
