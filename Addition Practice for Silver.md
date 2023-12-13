**Problem 2:** https://codeforces.com/problemset/problem/1359/C
Since the temperature is the average of the cups, we know that the temperature can be 2 choices at any given time. If the optimal time $n$ is even, we know that we have poured n/2 cups of hot water and n/2 cups
of cold water so the average is (h * n/2 + c * n/2)/n = (n(h+c)/2)/n = (h+c)/2. If $n$ is odd, we know that since we poured 1 cup of hot water first, we poured 1 cup of hot water last. Thus, the average is
(h * (n-1)/2 + c * (n-1)/2 + h)/n = (n-1)/n * (h+c)/2 + h/n.

We know that if days are even, then the avg temperature is always (x+y)/2. If the days are odd, the avg temperature is always (d(x+y) + x)/(2d+1) = 


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
