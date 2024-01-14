**Problem 1:** https://cses.fi/problemset/task/1650
Based on properties of XOR function we know that to to undo xor from a to b, we simply xor a and b again. Thus, we can build an xor prefix sum, then just un-xor the prefix a-1 from prefix b.

```cpp
void solve(){
    int n,q; cin>>n>>q;
    vi v; int pre[n+1];
    pre[0]=0;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        pre[i+1] = pre[i] ^ v[i];
    }

    while(q--){
        int a,b;cin>>a>>b;
        cout<<(pre[b]^pre[a-1])<<endl;
    }
}
```
**Problem 2:** https://codeforces.com/problemset/problem/1359/C
Since the temperature is the average of the cups, we know that the temperature can be 2 choices at any given time. If the optimal time $n$ is even, we know that we have poured n/2 cups of hot water and n/2 cups
of cold water so the average is (h * n/2 + c * n/2)/n = (n(h+c)/2)/n = (h+c)/2. If $n$ is odd, we know that since we poured 1 cup of hot water first, we poured 1 cup of hot water last. Thus, the average is
(h * (n-1)/2 + c * (n-1)/2 + h)/n = (n-1)/n * (h+c)/2 + h/n.

We know that if days are even, then the avg temperature is always (x+y)/2. If the days are odd, the avg temperature is always (d(x+y) + x)/(2d+1) = 

2013-12-13
Ok, we realize that odd moves is an monotonically decreasing function, and even moves is just (h+c)/2. Thus, we binary search for x: the minimum odd that went over t, so the first number of moves that the avg temperature exceeds t. We then know that the answer is either 2 or the moves on either side of t, so it would be 2, x-2, or x. Since x is the first number to the right of t so x-2 is the last number to the left of t. Thus, we can just check each of these and compare their answer.
**Please review the O(1) equation solution**
```cpp
void solve(){
    long double h,c,t;cin>>h>>c>>t;
    long lo = 1; long hi = 1000000005;
    while(lo < hi){
        long mid = lo + (hi - lo) / 2;
        long double ok = ((mid) * (h+c)+h)/(2*mid+1); // equation to calculate avg
        if(ok >= t){
            
            lo = mid+1;
        }
        else{

            hi = mid;
        }
    }
// checking each answer 2, x-2,x
    long double ok = (h+c)/2;
    int osa = 2*hi+1;
    long double mid = hi;
    long double ans = ((mid) * (h+c)+h)/(2*mid+1); 
    if(osa != 1 && t-ans >= ((mid-1) * (h+c)+h)/(2*mid-1)-t){
        ans = ((mid-1) * (h+c)+h)/(2*mid-1);
        osa-=2;
    }
    if(ans < t && t-ans >= t-((h+c)/2)){
        osa=2;
    }
    else if(ans >= t && ans-t >= t-((h+c)/2)){
        osa=2;
    }
    
    cout<<osa<<endl;
  


}
```
**Problem 3:** https://oj.uz/problem/view/CEOI12_jobs
Time: More than I should have

Mistakes:
- Didnt really read problem too clearly which costed like 30mins
- Came up with binary search easily
- Spent most time debugging since I didnt account for the case where if a request comes before the current chosen greedy day, we cannot process this request, so we must increase days to the current day


Self Editorial:
This is pretty similar to factory problem. We notice monotonicity of answer since more machines = better chance of accomplishing requests, so we can binary search.
It is always greedy for each tasks to squeeze in and use a robot if possible. 
We sort the tasks by request deadline since it is always more optimal to choose earlier tasks to complete in order to mazimize chances.
During binary search, we pick a machine to accomplish task if: there is an avaliable machine on that day and the day is >= request time since we cannot process a task before its request.
We then process and find min using binary search.

```cpp
void solve(){
    int n,d,m;cin>>n>>d>>m;
    vector <pi> v;
    rep(i,0,m){
        int u;cin>>u;v.pb({u,i});
    }
    sort(v.begin(),v.end()); // greedily choose earliest request time
    int lo = 0; int hi = 1000000005;
    hi++;
	while (lo < hi) {
		int mid = lo + (hi - lo) / 2;
        int day = 1;
        int cnt = 0;
        bool yn = true;
        rep(i,0,m){
            if(cnt == mid){ // we reached max compacity for this day
                // reset
                day++;
                cnt = 0; 
            }
            if(v[i].first > day){ // this task is here before the current day
		// we must make the day to the current day in order to process it
                day = v[i].first;
                cnt = 0;
            }
            if(day - v[i].first > d){ // past expiration
                yn = false;
            }
            cnt++;
        }
		if (yn) {
			hi = mid;
		} else {
			lo = mid + 1;
		}
	}
    int day = 0;
    int cnt = 0;
    bool yn = true;
    vi aj[n]; // for every day
    rep(i,0,m){
        if(cnt == hi){
            // reset
            day++;
            cnt = 0; 
        }
	if(v[i].first > day){
            day = v[i].first-1; // adjust for 0 indexed
            cnt = 0;
        }
        aj[day].pb(v[i].second);
        cnt++;
    }
    cout<<hi<<endl;
    rep(i,0,n){
        rep(j,0,aj[i].size()){
            cout<<aj[i][j]+1<<" ";
        }
        cout<<0<<endl;
    }
    
}
```
**Problem 4:** https://codeforces.com/contest/803/problem/D
We can binary search on answer(maximum length of the ad). We fix this length, then any line cannot go past this length. We then simulate fitting the strings greedily, trying to fit the maximum possible string length without exceeding aans length. Then check if its possible to fit everything in it without exceeding k lines. The simulation is kinda weird & messy cause you have to deal with strings, but idea is simple.

```cpp
void solve(){
    int k;cin>>k;
    string s;
    vector <string> v;
    while(cin>>s){
        v.pb(s);        
    }
    int lo = 0; int hi = 1000000;
    while(lo < hi){
        int mid = lo + (hi - lo + 1) / 2;
        int cnt = 1; // number of lines needed
        int osa = 0; // total characters per line
        int nosa = 0; // characters of current segment
        rep(i,0,v.size()){
            rep(j,0,v[i].size()){
                if(v[i][j] == '-'){ // see if we begin a new line
                    nosa++; 
                    if(osa + nosa <= mid){ // we can keep segment on curr line
                        // reset
                        osa += nosa; // append length to curr line
                        nosa = 0; // set cur seg length = 0
                    }
                    else{
                        if(nosa > mid){ curr segment doesnt fit on line, forced to make new line
                            cnt = 1000000000; // set to INF = impossible
                        }
                        else{ // start new line
                            cnt++;
                            osa = nosa;// append curr length to new line
                            nosa = 0; // set curr seg length to 0
                            
                        }
                    }
                }
                else{
                    nosa++;
                }
            }
            if(i != v.size()-1){
                // last space
                nosa++;
                
            }
            if(osa + nosa <= mid){ // we can keep segment on curr line
                // reset
                osa += nosa;
                nosa = 0;
            }
            else{ // curr segment doesnt fit on line, forced to make new line
                if(nosa > mid){ // curr seg > mid(max seg), cant make new line
                    cnt = 1000000000;
                }
                else{ // start new line
                    cnt++;
                    osa = nosa; // append curr length to new line
                    nosa = 0; // set curr seg length to 0
                   
                }
            }

        }
        if(cnt <= k){ // would work
            hi = mid;
        }
        else{
            lo = mid+1;
        }
    }
    cout<<hi<<endl;
    
}
    
```

**Problem 5:** https://codeforces.com/contest/1077/problem/D
We can binary search for the answer(number of copies possible), since we can have a greedy method. We sort the integers by the number of appearences. Thus if we fix that we are taking x copies, then for each integer v_i there must be (number of v_i)/x appearences in our final answer array. We know that the answer is monotonic, so we just check if at the end the size of our final answer array is >= k or not.
```cpp
void solve(){
    int n,k;cin>>n>>k;
    map <int,int> mp;
    vi v;
    rep(i,0,n){
        int u;cin>>u;mp[u]++;
        v.pb(u);
    }
    set <pi> st;
    rep(i,0,v.size()){
        st.insert({mp[v[i]], v[i]});
    }

    int lo = 0; int hi = n;
    while(lo < hi){
        int mid = lo + (hi - lo) / 2; // number of copies
        int cnt = 0;
        for(auto it = st.begin();it != st.end();it++){
            pi ok = *it;
            cnt += ok.first/mid;
        }
        if(cnt >= k){ // works
            lo = mid;
        }
        else{
            hi = mid-1;
        }

    }

    vi ans;
    for(auto it = st.begin();it != st.end();it++){
        pi ok = *it;
        rep(i,0,ok.first/lo){
            if(ans.size() == k){
                break;
            }
            ans.pb(ok.second);
            
        }
            
    }
    rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }

}
```

**Problem 6:** https://codeforces.com/problemset/problem/1223/C
We can binary search the positions, since bigger position always means smaller answer. We note that the greedy method is always try to put the largest at any positions where they both intersect, then choose whether x or y is larger then put the next biggest v_i at the each ath or bth position unvisited. We then check in the end whether the sum is bigger than k or not and do binary search. Note we have to add long long to everything for this to AC.
```cpp
void solve(){
    ll n;cin>>n;
    vector <ll> v;
    rep(i,0,n){
        ll u;cin>>u;v.pb(u);
    }
    
    sort(v.begin(),v.end());
    ll x,a,y,b;cin>>x>>a>>y>>b;
    ll k; cin>>k;
    ll lo = 0; ll hi = n;
    hi++;
    while(lo < hi){
        ll mid = lo + (hi - lo) / 2; // position
        ll vis[mid]={0};
        ll ab = lcm(a,b);
        ll it = v.size()-1;
        ll ans = 0;
        // first pick position where a and b intersect
        for(ll i = ab-1;i<mid;i+=ab){
            vis[i] = 1;
            ans += v[it]/100 * (x+y);
            it--;
        }
        // choose next largest
        if(x > y){
            for(ll i = a-1;i<mid;i+=a){
                if(vis[i] == 0){
                    vis[i] = 1;
                    ans += v[it]/100 * x;
                    it--;
                }   
            }
            for(ll i = b-1;i<mid;i+=b){
                if(vis[i] == 0){
                    vis[i] = 1;
                    ans += v[it]/100 * y;
                    it--;
                }   
            }
        }
        else{
            for(ll i = b-1;i<mid;i+=b){
                if(vis[i] == 0){
                    vis[i] = 1;
                    ans += v[it]/100 * y;
                    it--;
                }   
            }
            for(ll i = a-1;i<mid;i+=a){
                if(vis[i] == 0){
                    vis[i] = 1;
                    ans += v[it]/100 * x;
                    it--;
                }   
            }
        }
        if(ans >= k){ // works
            hi = mid;
        }
        else{
            lo = mid + 1;
        }
    }
    if(hi == n+1){ // didnt find ans
        cout<<-1<<endl;
    }
    else{
        cout<<hi<<endl;
    }
}
```

**Problem 11:** https://codeforces.com/contest/1468/problem/D

Time: 20 minutes ish, kinda wacky with greedy sol and a little bit with the math for how many moves 

Self Editorial:
I thought this was not that hard. We note that we never want to go towards the guard. Then, we want to waste as much time as possible. If we start by moving in the opposite direction without placing down any fire crackers, we have not increased the distance between us and the guard, and not wasted any time since we didnt place down any fire crackers. Moreover, we decreased the distance from us to the border which means this move was just bad for us. Instead, we should place as many fire crackers as possible before we are forced to move. We then waste as much time as possible by moving until we reach the border, always maintaining dist of 1 with the guard. Now its left to determine which fire crackers to place. We note that earlier placed firecrackers will explode earlier. So we want to maximize the time of earlier firecrackers. We can go through the array, and see if the firecracker will explode if it is set at the current time. If it will, we increase current time by 1(since we set this fire cracker), and continue. Else, we don't use this firecracker at all since it will be a waste of time.
**Formal proof of why this choosing works?**
```cpp
void solve(){
    int n,m,a,b;cin>>n>>m>>a>>b;
    vi v;
    rep(i,0,m){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end());
    if(a<b){
        int cnt = b-a-1; // seconds hooligan can stay at a
        int ans =0;
        int it = 0;
        for(int i = m-1;i>=0;i--){ 
            
            if(it+1 <= cnt){ // math to see if our current time exceeds the max time we can stay
                
                if(b - 1 - (it+1) >= v[i]){ // math: if this firecracker will explode before the guard catches us if we set it at this time
                // explode <= total time - curr time = time for guard to get to border - current time
                    it++; // increase time
                    ans++; // increase ans
                }
            }
        }
        cout<<ans<<endl;
    }
    else{ // other case where a > b, we must go from a to border n
        int cnt = a-b-1; // seconds hooligan can stay at a
        int ans =0;
        int it = 0;
        for(int i = m-1;i>=0;i--){ 
            
            if(it+1 <= cnt){
                if(n-b - (it+1) >= v[i]){
                    it++;
                    ans++;
                }
            }
        }
        cout<<ans<<endl;
    }
}
   
```

**Problem 13:** https://codeforces.com/contest/51/problem/C

Time: pretty bad like it was free but impl was not good cause **I didnt really get how eps work in binary search, like i had a but where I set long double pos = v_i + mid, but then pos - mid <= v_i failed or smth. So I didnt really get how binary search with precision worked and that costed a lot of time ig.

Self Editorial:
This is pretty much like CCC S3 Lunch Concert but you only have 3 stations( this can also be generalized with k stations using bianry search as well). Pretty much just fix the radius of the station using binary search(since answer is monotonic because bigger radius = more chance to succeed), then go through each house and greedily assign station(put new station only if its absolutely necessary). Then in the end check if number of stations added exceeded 3.
```cpp
void solve(){
    int n;cin>>n;
    vector<long double> v;
    rep(i,0,n){
        long double u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end());
    long double lo = 0; long double hi = 1000000005;
    while(lo + 0.000000001 < hi){ // eps = 10^-9
        long double mid = lo + (hi - lo) / 2;
        int cnt = 1;
        long double pos = v[0]+mid; // must put a station at this position to fit in v[0]
        rep(i,0,n){
            if(pos - mid-0.000000001<= v[i] && v[i] <= pos + mid+0.000000001){ // precision eps stuff
                // alreay contained in current station
            }
            else{
                cnt++; // add station
                
                pos = v[i]+mid; // new station position
                
            }
        }
        if (cnt <= 3) { // works, number of station used smaller than 3
			hi = mid;
		} else {
			lo = mid + 0.000000001;
		}
    }
    cout << fixed << setprecision(9) <<hi<<endl;
    long double pos = v[0]+hi;
    cout<<pos<<" ";
    int cnt = 1;
    // simulate to get station pos since we already know min radius
    rep(i,0,n){
        if(pos - hi -0.000000001<= v[i] && v[i] <= pos + hi+0.000000001){ // precision eps stuf
                // alreay contained in current station
        }
        else{
            
            cnt++; // add new station
            pos = v[i]+hi;
            cout<<pos<<" ";
        }
    }
    while(cnt < 3){ // if there are extra stations left over, we add any station
        cout<<2000000000<<" ";
        cnt++;
    }
    
}
```

**Problem 18:** https://codeforces.com/contest/404/problem/C
We note that the $i$ where $d[i] = 0$ is the node that the guy chose. We have to construct a graph where the distance from $i$ to another node $x$ is $d[x]$.

We also note that this is a tree structure. Any cycle would make the node more probable to have more than k edges and also not help the distance, thus its always less optimal to have cycle.

**Problem 19:** https://codeforces.com/contest/1288/problem/D

**Self Editorial:**
Since m <= 8, we use bits. 
We can binary search for answer. We represent every array $a_i$ as number in binary(8 positions). The position is 1 if $a[i][j] >= mid$. We take two arrays, and for each positions we take the max. Thus, if either of the two arrays have a 1 in that position, we know the resulting $b[pos] \ge mid$. Thus, mid works if $binary[a_i] | binary[a_j] = 2^m-1$, meaning the OR of the two binary make every position 1. This means that max of every position must always >= mid.
**Trick I was stuck on:**
Comparing pairs takes O(n^2) which is too slow. Instead, we can go through every possible pair of number in binary possible, since the numbers are $0...2^{m}-1$ which means there is at most 256 numbers and 256 * 256 pairs. For each pair i and j, we check if $i|j = 2^m-1$. If it is, we check if i and j are both the binary representation of some array given. If it is, we know an array $a_i$ exists for both i and j meaning that mid is valid.
```cpp
void solve(){
    int n,m;cin>>n>>m;
    int a[n][m];
    rep(i,0,n){
        rep(j,0,m){
            cin>>a[i][j];
        }
    }
    int lo = 0; int hi = 1000000005;
    lo--;
    pi ans;
    // BINARY SEARCH MAX ANS
    while (lo < hi) {
		// find the middle of the current range (rounding up)
		int mid = lo + (hi - lo + 1) / 2;
        int arr[(1<<m)]={0}; // indicies of osa value i
        rep(i,0,n){
            int two = 1; // power of twos
            int osa = 0; // a[i] in binary representation
            rep(j,0,m){
                if(a[i][j] >= mid){ // max(smth, a[i][j]) >= mid
                    osa += two; // add 1 to that position
                }
                two *=2;
            }
            arr[osa]=i+1; // an array with binary = osa exists at index i+1

        }
        bool yn = false;
        rep(i,0,(1<<m)){
            rep(j,0,(1<<m)){
                if((i|j) == (1<<m)-1 && arr[i] && arr[j]){ // i OR j = 2^m-1, binary representation of i and j both exist for some a[i]
                    ans = {arr[i],arr[j]}; // we found answer
                    yn = true;
                }
            }
        }
		if (yn) {
			// if mid works, then all numbers smaller than mid also work
			lo = mid;
		} else {
			// if mid does not work, greater values would not work either
			hi = mid - 1;
		}
	}
    cout<<ans.first<<" "<<ans.second<<endl;
} 
```

**Problem 22:** https://codeforces.com/contest/1744/problem/F

Observations:
Given the mex = x, mex > med if sz/2 <= mex. We know this by testing specific cases.
Lets go through each mex and find how many subarrays mex > med.


**Problem 24:** https://codeforces.com/contest/412/problem/D

Observations:
From the n = 3 case, we list out
123, 132, 213, 231, 312, 321. What im getting at is: choosing any 3 possible pairings still guarentees an answer. But idk how to prove this or how to find this answer.

Working with some cases, it looks like the following strategy will always work:
Given list of pairs, we flip it so that now we're left with pairs that we WANT. Now, we go through the first number in the pair. We then insert number based on its frequency as the first number. Therefore, if the flipped pairs were
4,1
2,3
4,2
4,3
2,1
1,3
our answer would be 4213.
I don't know why this works but I havent found a counter yet. Might as well just code it up and test luck cause its getting late.

Ok i just saw editorial and i think i should actually get a diagonsis like damn how am i so like clueless jeez. idk man its like my brain is just like so tiny its wild bruh.
This is just like simple bruteforce that completely went over my head. I was tryna come up with some niche smart stuff but actually is just free braindead brute like bruh might just go keep myself safe after this one. 
**Self Editorial:**
If two numbers in the permutation are next to each other but are not supposed to be, like in the m pairs we got (a,b) but ab is in the permutation, we can just swap so now we have ba. Then, ba is satisfied since (a,b) and (b,a) cannot be in the m pairs at the same time(in problem statement). Then, the permutation is cba and we only have to worry if (c,b) is in the m pairs. If it is, we can **keep on swapping until we have b at the front**.
Since everything previous is satisfied, this permutation is satisfied.
To do this, we append each number one by one. Then, we'll swap until we have everything satisified(adding b and swapping until we have cba where (c,b) is not a pair or adding b until it reaches the front, where all prev pairs is satisfied). 
Heres the part which broke me from thinking brute force i think but is actually key: there are only m illegal pairs, so at most m swaps needed. This means total time is O(n+m).

**Reflection:**
I thought this thinking is very straight foward and easy but idk why I couldnt get it. Even now idk if I wouldve gotten it. I think I shouldve thought about what make it illegal and how we can change that if we swap, like a DP thinking ig.

**NOTE: PLEASE REVIEW DFS SOLUTION**
```cpp
void solve(){
    int n,m;cin>>n>>m;
    map <pi,int> mp; // is pair illegal?
    rep(i,0,m){
        int a,b;cin>>a>>b;
        mp[{a,b}]=1;
    }
    vi ans;
    rep(i,1,n+1){
        ans.pb(i);
        int it = i-2; // number in front of curr
        while(it >= 0 && mp[{ans[it],i}] == 1){
            ans[it+1]=ans[it];
            ans[it]=i;
            it--;
        }
    }
    rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }
    

}  
```
