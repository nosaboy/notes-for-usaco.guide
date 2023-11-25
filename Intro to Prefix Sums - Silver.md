# Introduction to Prefix Sums
### Usage
Given integer array a, query the sum of any range(l,r) [l...r] 
precomputation: O(n)
processing: O(1)


### Example 1

https://judge.yosupo.jp/problem/static_range_sum
- Basic prefix sum implementation
- Precompute all prefix of sums
- The sum of range [l,r] inclusive will be pre[r] - pre[l-1]

```cpp
void solve(){
    int n;
    cin>>n;
    int q;
    cin>>q;
    ll a[n];
    rep(i,0,n){
        cin>>a[i];
    }
    ll pre[n+1];
    pre[0] = 0; 
    rep(i,1,n+1){
        pre[i] = a[i-1] + pre[i-1];
    }
    
    while(q--){
        int l,r;
        cin>>l>>r; l++;
        cout<<pre[r] - pre[l-1]<<endl;
    }
}
```

### Problems
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=572
We can just keep track of the sum of the number of cows for each ID in a certain interval using prefix sums. 
For every cow at $a[i]$, we will add 1 to the prefix sum
```cpp
void solve(){
    int n;
    cin>>n;
    int q;
    cin>>q;
    int pre[3][n+1]; //ID, index
    pre[0][0] = 0;
    pre[1][0] = 0;
    pre[2][0] = 0;
    
    rep(i,0,n){
        pre[0][i+1] = pre[0][i];
        pre[1][i+1] = pre[1][i];
        pre[2][i+1] = pre[2][i];
        int u; cin>>u; u--;
        pre[u][i+1]++;
    }

    
    while(q--){
        int l,r;
        cin>>l>>r; 
        cout<<pre[0][r] - pre[0][l-1]<<" "<<pre[1][r] - pre[1][l-1]<<" "<<pre[2][r] - pre[2][l-1]<<endl;
    }
}
```

**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=595
If their IDs are a multiple of 7, it must mean that $pre[r] - pre[l-1] \equiv 0 \pmod{7}$ which means $pre[r] \equiv pre[l-1] \pmod{7}$
To find the max, we just need to find the maximum length for each difference from 0 to 6 which means finding the min and max index such that their prefix have the same mod 7.
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
    vi v[7];
    rep(i,0,n+1){
        v[pre[i]%7].pb(i);
    }
    int ans = 0;
    rep(i,0,7){
        sort(v[i].begin(),v[i].end());
        if(v[i].size()){
            ans = max(ans, v[i][v[i].size()-1] - v[i][0]);
        }
        
    }
    cout<<ans<<endl;
}

```
**Problem 3:** http://www.usaco.org/index.php?page=viewproblem2&cpid=691
We can just check all cases. We first maintain a prefix sum that stores how many each move is used by Farmer John. We then check all 3 options for Bessie, then for each choice we loop over and fix our switching point. After switching we just pick the max option that farmer john picked since we know we can always counter it.
```cpp
void solve(){
    int n;
    cin>>n;
    int pre[3][n+1];
    pre[0][0] = 0;
    pre[1][0] = 0;
    pre[2][0] = 0;

    rep(i,0,n){
        char c; cin>>c;
        pre[0][i+1] = pre[0][i];
        pre[1][i+1] = pre[1][i];
        pre[2][i+1] = pre[2][i];
        if(c== 'H'){
            pre[0][i+1]++;
        }
        if(c== 'P'){
            pre[1][i+1]++;
        }
        if(c== 'S'){
            pre[2][i+1]++;
        }
    }
    int ans = 0;
    rep(i,0,3){ //check over all possible combinations
        rep(j,0,3){
            rep(l,1,n+1){ // fixed switching point
                ans = max(ans, pre[i][l] - pre[i][0] + pre[j][n] - pre[j][l]);
            }
        }
    }
    cout<<ans<<endl;
    
}
```
**Problem 4:** https://cses.fi/problemset/task/1661
Lets fix the ending point of some subarray by looping each index from 0 to n-1. If $pre[i] = a$ for some a, in order for the final sum of a subarray $[l,i]$ to be x we must have $pre[l-1] = a-x.$ We maintain a map which counts every previous prefix such that $pre[l-1] = a-x$ so $mp[a-x]$ is the number of prefixes = $a-x.$ Thus, the number of valid subarrays ending in i is just $mp[a-x].$

```cpp
void solve(){
    int n;
    ll x;
    cin>>n>>x;
    ll pre[n+1];
    pre[0] = 0;
    map <ll,ll> mp;
    mp[0] = 1;
    ll ans = 0;
    rep(i,0,n){
        ll u;
        cin>>u;
        pre[i+1] = pre[i] + u;
        ans += mp[pre[i+1] - x];
        // we do this after since we might count pre[r] - pre[r] = 0 which
        // is just an empty subarray which is not valid
        mp[pre[i+1]]++;
    }
    cout<<ans<<endl;
    
}
```

**Problem 5:** https://cses.fi/problemset/task/1662/
This is pretty much just an extension of subsequently counting by 7. The thing is you have to count number of subarrays. Thus for every x mod n, you must pick 2 points l-1 and r from the k points that are $pre[i] \equiv x \pmod{n}.$ So there will be $\binom{k}{2}$ ways for every x. One other thing to note is that when modding negatives, make sure you ==first make it positive by adding some big multiple of n== THEN MOD( this is how I got my double WA).
```cpp
void solve(){
    ll n;
    cin>>n;
    ll pre[n+1];
    pre[0] = 0;
    rep(i,0,n){
        ll u;
        cin>>u;
        pre[i+1] = pre[i] + u;
 
    }
    ll v[n]={0};
    rep(i,0,n+1){
        v[(pre[i]+1000000000LL*n)%n]++; // add a big multiple of n so it returns the correct mod
    }
    ll ans = 0;
    rep(i,0,n){
        ans += v[i]* (v[i] - 1)/2;
    }
    cout<<ans<<endl;
}
 
```

**Problem 6:** http://www.usaco.org/index.php?page=viewproblem2&cpid=715
We can brute force every length k and find how many broken lights are in there, then take the minimum number of broken lights for every k segment. This will be the minimum light we need to fix. We can compute the numer of broken lights in a range using prefix sums by adding 1 if light is broken and 0 if it is good, then prefix sum everything up. Since N <= 10^5, this will pass easily.

```cpp
void solve(){
    int n,k,m;
    cin>>n>>k>>m;
    int pre[n+1]={0};
    pre[0] = 0;

    rep(i,0,m){
        int u;
        cin>>u;
        pre[u]++;
   
    }
    rep(i,1,n+1){
        pre[i] += pre[i-1];
    }
    int ans = 1000000005;
    rep(i,0,n-k+1){
        int osa = pre[i+k] - pre[i];
        ans = min(ans, osa);
    }
    cout<<ans<<endl;
}
```

**Problem 7:** https://www.spoj.com/problems/HAYBALE/ **NOTE I DIDNT SOLVE THIS PROBLEM I JUST SAVED MY CODE HERE**
Since n <= 10^6, we can just find all possible values in array a and then find the median using sorting(maybe this will tle so idk might use binary search as well). To simulate the process of adding haybales, we can just add 1 to start of a and -1 to b+1, then prefix sum the whole array after adding. This will make 000011111...111110000
    a^          b^
so that each stack in the interval has 1 added to it.
```cpp
void solve(){
    int n,q;
    int pre[n+2]={0};
    cin>>n>>q;
    while(q--){
        int l,r;
        cin>>l>>r;
        pre[l]++;
        pre[r+1]--;
    }
    rep(i,1,n+1){
        pre[i] += pre[i-1];
    }
    sort(pre,pre+(n+2));
    cout<<pre[n/2+1]<<endl;
}  
```

**Problem 8:** https://codeforces.com/contest/1398/problem/C
If the sum of an array length n is n, the average of the array is 1.
A **common trick** when dealing with averages:
- If we decrease all values in the array by the average n/k, a subarray with sum n and average n/k will now become 0.
- So now we only need to find all subarrays whose sum is 0 using prefix sums.
```cpp
void solve(){
    int n;
    string s;
    cin>>n>>s;
    vi v;
    rep(i,0,n){
        v.pb(s[i]-'0' - 1);
    }
    int pre[n+1];
    pre[0] = 0;
    rep(i,1,n+1){
        pre[i] = pre[i-1] + v[i-1];
    }
    map <int,int> mp;
    mp[0]++;
    ll ans = 0;
    rep(i,1,n+1){
        ans += mp[pre[i]]; //using map to find sum like subarray sum ii
        mp[pre[i]]++;
    }
    cout<<ans<<endl;
}
```


**Problem 9:** https://atcoder.jp/contests/abc125/tasks/abc125_c
We can just fix which number we want to replace, then find the best number we can replace it with in o(1) time. We know that gcd(x,y) cannot be bigger than x or y no matter what they are. Thus, the largest value of gcd(prev, new num) = prev since we can freely choose new num and the gcd value will only get smaller and not larger. We can precompute the value of gcd(all values except for a_i) using "prefix gcd" and "suffix gcd" then find the maximum possible value i can be in order to maximize gcd. Then max that value over all fixed i to get ans.
```cpp
void solve(){
    int n; cin>>n;
    vi v;
    rep(i,0,n){
        int u;
        cin>>u;
        v.pb(u);
    }
    int pre[n+1]={0};
    int suf[n+1]={0};
    pre[0] = -1;
    pre[1] =  v[0];
    rep(i,2,n+1){
        pre[i] = gcd(pre[i-1], v[i-1]);
    }

    suf[n] = -1;
    suf[n-1] = v[n-1];
    for(int i = n-2;i>=0;i--){
        suf[i] = gcd(suf[i+1], v[i]);
    }
    int ans = 0;
    rep(i,1,n+1){
        int osa;
        //corner cases for border:
        if(pre[i-1] != -1){
            osa = pre[i-1];
        }
        if(suf[i] != -1){
            osa = suf[i];
        }
        //set it to gcd()
        if(pre[i-1] != -1 && suf[i] != -1){
            osa = gcd(pre[i-1],suf[i]);
        }
        ans = max(ans, osa);
    }
    cout<<ans<<endl;
    
}   
```


**Problem 10:** https://codeforces.com/contest/1826/problem/D
I solved this problem before. 


**Problem 11:** https://codeforces.com/contest/1291/problem/D
Just by looking at the testcases, I predict it will be NO if all characters in the array are the same and YES otherwise.
Nah bro I struggled on this problem a lot cause its like those smart but low rated observations. 
I think I was just really stuck on trying to think of the letters in string s being attached to letters in string t, but this is not the correct approach cause it really fucking confused me for a long time cause I couldn't really visualize & prove stuff. I probably should've just found another way to determine whether or not a string is irreducible. The editorial thought that if the prefix of the two strings are never equal until the very end, then it is always irreductible. After this, we can just try to think about ways to construct a string such that the prefix irreductible.
If we have the last character of substr differ from first character, we know that we can put all letters of the last character in front, so that the prefix of t will always have >= 1 appearences of that letter since front != back, so the first letter differs. 
If the first and last characters are the same, we know that length 1 arrays are always valid. **Now, this is the strat I think I couldn't come up with on my own**: Let the number of distinct characters be >= 3. We can then use the following strat: first pick the last occuring distinct letter, then put all of them in the front. If we were to have a prefix s = t, the index must be bigger than the last index of that distinct letter. We then know that there must be 2 distinct letters in the prefix of this. We then put every occurance of the ending letter. This will prevent it from having the same prefix since we know s has 3 distinct values in its current prefix while t only has 2, so this construction will always be valid(holy fk this is so hard to explain im going to cry).
If distinct characters = 1 or 2 and first = last, we know that for the 1 case if length != 1 it is always reversible since there is only one construction. If distinct characters = 2 we have the starting character s != starting character t. WLOG let s only have a and b and that s starts and ends with a. We know that to contruct t, we must have t begin with b. Now, we are going to use **proof by contradiction**. Assume that s is irreversible. Since s ends with an a, we know that there must exist a point $i$ before the end of s in which the number of bs in s is >= the number of bs in t. This means that at some point, it must be so that both s and t have equal bs since s can only increase by 1 each time and the bs in t was bigger than the bs in s before since starting of s = a and starting of t = b. Thus, this means s is reversible since if bs in s = bs in t then as in s = as in t, which is a contradiction.

^ I feel like especially this last part of the observation is pretty godly and I sure as hell wouldnt have come up with it myself and I dont even know how you can think of that stuff up. This is like more difficult then some of the observations im making for 2000s so idek at this point how its 1800.
```cpp
void solve(){
    string s; cin>>s;
    int pre[26][s.size()+1]={0};
    memset(pre,0,sizeof(pre));
    rep(i,0,s.size()){
        pre[s[i]-'a'][i+1]++;
    }
    rep(i,1,s.size()+1){
        rep(j,0,26){
            pre[j][i] += pre[j][i-1];
        }
    }
    int q;
    cin>>q;
    while(q--){
        int l,r; cin>>l>>r;
        int cnt = 0;
        rep(i,0,26){
            if(pre[i][r] - pre[i][l-1]){
                cnt++;
            }
        }

        if(cnt < 3 && s[l-1] == s[r-1]  && l != r){
            cout<<"No\n";
        }
        else{
            cout<<"Yes\n";
        }
    }
}
```
