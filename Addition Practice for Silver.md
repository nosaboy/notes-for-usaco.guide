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
