#### Unordered Sets
A set but elements are in a random order instead of sorted so we don't know the next highest value. Works by hashing, meaning **high constant factor**: 
assigning code to specific value.
- Every operation is O(1) instead of set which is O(logn)
- Except for .erase() and .clear() which is O(n)

```cpp
unordered_set<int> st;
st.insert(1); // [1]
st.insert(2); // [1, 2] in arbitrary order
cout<<st.count(1)<<endl; // 1
st.erase(1); // [2]
```
#### Unordered Maps
Map but it is not sorted by key(mp[key] = value) so we don't know the next highest key. Works by hashing, meaning **high constant factor**.
- Every operation is O(1) instead of O(logn)
- Supports insert: mp[key] = value, erase: mp.erase(key), count: mp.count(key)
```cpp
unordered_map<int, int> m;
m[1] = 5; // [(1, 5)]
m[3] = 14; // [(1, 5); (3, 14)]
m.erase(3); // [(1, 5)]
cout << m[1] << '\n'; // 5
cout << m.count(1) << '\n' ; // 1
```
**Can use unordered in USACO. Don't use unordered in codeforces**

#### Iterators
A variable that points of a element in the data structure.
auto it = st.begin() points to first element in data structure
auto it = st.end() points to position after the last element
Access the element that the iterator is pointing to using "*": *it
Loop through set
```cpp
for (auto it = st.begin();it != st.end(); it++){
  cout<<*it<<endl;
}
```  
Ordered(sorted) Sets and Maps supports
- auto it = st.lower_bound(x): returns iterator to the smallest value/key **greater or equal** to x
- auto it = st.upper_bound(x): returns iterator to the smallest value/key **greater** than x

#### Priority Queue(heap)
Supports all operations of sets: insertion of element, deletion of highest priority element, retrieval of highest priority element.
```cpp
priority_queue<int> pq;
pq.push(2); // [2]
pq.push(1); // [1,2]
pq.push(3); // [1,2,3]
cout<<pq.top()<<endl // 3
pq.pop(); // [1,2]
```

### Problems

**Problem 1:** https://cses.fi/problemset/task/1091
Simulate the scenario with a multiset or priority queue. 
**Problem 2:** https://codeforces.com/contest/702/problem/C
Did this problem, but we can just use binary search and simulate the process

**Problem 3:** https://cses.fi/problemset/task/1163/
We can still simulate the process. We store all the lengths and all the traffic lights. After each query, the only length that changes is the traffic lights on either side of the added traffic lights. We then erase the previous length between the 2 traffic lights and add the 2 new lengths that split in the lengths multiset. We then take the maximum length of all lengths. 
```cpp
void solve(){
    int x,n; cin>>x>>n;
    multiset <int> ms; // positions
    multiset <int> len; // lengths
    ms.insert(0);
    ms.insert(x);
    // length between two traffic lights x and y is y - x
    len.insert(x);
    while(n--){
        int a;
        cin>>a;
        auto l = ms.upper_bound(a);
        --l;
        auto r = ms.upper_bound(a);
        int length = *r - *l;
        len.erase(len.find(length));
        len.insert(a - *l);
        len.insert(*r - a);
        ms.insert(a);
        auto ans = len.end();
        --ans;
        cout<<*ans<<" ";
    }
}
```
**Problem 4:** https://cses.fi/problemset/task/1164
I think this is some greedy where we just simulate the process. For every customer, we give the earliest room that is open. If no rooms are open at the time, we must make a new room. This works because if we don't give the earliest room it will be worse and we will have to make a new room anyway.


**Example 1:** https://cses.fi/problemset/task/1188/
I def overkilled this problem so hard and spent like an hour doing every case using multisets. This is pretty much just traffic lights but with more cases to work with. **There is def a easier way tho...**
```cpp
void solve(){
    string s; cin>>s;
    int q; cin>>q;
    int start = 0;
    multiset <pi> ms;
    multiset <int> len; 
    rep(i,0,s.size()-1){
        if(s[i] != s[i+1]){
            ms.insert({start,i});
            start = i+1;

        }

    }

    if(s.size() == 1){ // special case not covered by official testcases on cses
        while(q--){
            int x;
            cin>>x;
            cout<<1<<" ";
        }
        cout<<endl;
        return;
    }
    ms.insert({start,s.size()-1});
    for(auto itr = ms.begin();itr != ms.end();itr++){
        pi osa = *itr;
        len.insert(osa.second - osa.first + 1);
     
    }
    while(q--){
        int x; cin>>x; x--;
        auto it = ms.upper_bound({x,1000000005});
        --it;
        pi ok = *it;
        
        if(ok.first < x && x < ok.second){ // in the middle
            
            ms.erase(ms.find(*it));
            ms.insert({ok.first,x-1});
            ms.insert({x,x});
            ms.insert({x+1,ok.second});

            len.erase(len.find(ok.second - ok.first + 1));
            len.insert(x - ok.first);
            len.insert(1);
            len.insert(ok.second - x);
        }
        else if(ok.first == ok.second){ // length 1 edge case
            if(it == ms.begin()){ // first
                ms.erase(ms.find(*it));
                auto itr = ms.begin();
                pi nok = *itr;
                ms.erase(ms.find(*itr));
                ms.insert({0,nok.second}); // merge the first 2 together

                len.erase(len.find(ok.second - ok.first + 1));
                len.erase(len.find(nok.second - nok.first + 1));
                len.insert(nok.second + 1);
            }
            else{
                ++it;
                if(it == ms.end()){ // very right
                    --it;
                    ms.erase(ms.find(*it));
                    auto itr = ms.end();
                    --itr;
                    pi nok = *itr;
                    ms.erase(ms.find(*itr));
                    ms.insert({nok.first, s.size()-1}); // merge the first 2 together

                    len.erase(len.find(ok.second - ok.first + 1));
                    len.erase(len.find(nok.second - nok.first + 1));
                    len.insert(s.size() - nok.first);

                }
                else{
                    pi osa = *it;
                    --it;
                    --it;
                    pi nosa = *it;
                    ++it;
                    ms.erase(ms.find(*it));
                    ms.erase(ms.find(osa));
                    ms.erase(ms.find(nosa));
                    ms.insert({nosa.first,osa.second});

                    len.erase(len.find(ok.second - ok.first + 1));
                    len.erase(len.find(osa.second - osa.first + 1));
                    len.erase(len.find(nosa.second - nosa.first + 1));
                    len.insert(osa.second - nosa.first + 1);
                }
            }
        }
        else{ // on the border
            
            if(ok.first == x){ // left
            
                if(it == ms.begin()){
                    
                    pi osa = *it;
                    ms.erase(ms.find(*it));
                    ms.insert({0,0});
                    ms.insert({1,osa.second});

                    len.erase(len.find(ok.second - ok.first + 1));
                    len.insert(1);
                    len.insert(osa.second);
                }
                else{
                    
                    auto itr = it;
                    --itr;
                    pi osa = *itr;
                    pi nosa = *it;
                    ms.erase(ms.find(*it));
                    ms.erase(ms.find(*itr));
                    ms.insert({osa.first,osa.second+1});
                    ms.insert({nosa.first+1,nosa.second});

                    len.erase(len.find(osa.second - osa.first + 1));
                    len.erase(len.find(nosa.second - nosa.first + 1));
                    len.insert(osa.second - osa.first + 2);
                    len.insert(nosa.second - nosa.first);
                }
            }
            else{
                
                ++it;
                if(it == ms.end()){
                    --it;
                    pi osa = *it;
                    ms.erase(ms.find(*it));
                    ms.insert({s.size()-1,s.size()-1});
                    ms.insert({osa.first,s.size()-2});

                    len.erase(len.find(ok.second - ok.first + 1));
                    len.insert(1);
                    len.insert(s.size()-osa.first-1);

                }
                else{
                    --it;
                    
                    auto itr = it;
                    ++itr;
                    pi nosa = *itr;
                    pi osa = *it;
                    ms.erase(ms.find(*it));
                    ms.erase(ms.find(*itr));
                    ms.insert({osa.first,osa.second-1});
                    ms.insert({nosa.first-1,nosa.second});

                    len.erase(len.find(osa.second - osa.first + 1));
                    len.erase(len.find(nosa.second - nosa.first + 1));
                    len.insert(osa.second - osa.first);
                    len.insert(nosa.second - nosa.first + 2);
                
                }

                
            }
        }
        auto ans = len.end();
        --ans;
        cout<<*ans<<" ";
      
    }
    

}
```
**Problem 5:** http://www.usaco.org/index.php?page=viewproblem2&cpid=763
Note that the scoreboard only changes if one of the 3 scenario occurs:
- if a cow was not on the scoreboard before, but now is on the scoreboard.
- if a cow was on the scoreboard before, but fell off
- if a cow was on teh scoreboard with multiple cows, but now the cow exceeded every cow to become the only cow on the scoreboard.
We simulate the scenario using a multiset, keeping track of who is currently the leader and if the number of cows on the scoreboard is bigger than 1. Make sure to also insert({g,0}) to account for all the other cows that have a score of g during the whole simulation.
```cpp
void solve(){
    ll n,g; cin>>n>>g;
    vector <tuple<ll,ll,ll>> v;
    map <ll,ll> mp;
    set <pair<ll,ll>> ms;
    set<ll> st;
    rep(i,0,n){
        ll a,b,c;
        cin>>a>>b>>c;
        mp[b] = g;
        st.insert(b);
        v.pb({a,b,c});
    }
    for(auto it = st.begin();it != st.end();it++){
        ms.insert({g, *it});
    }
    ms.insert({g,0});
    mp[0] = g;
    sort(v.begin(),v.end());
    ll ans =0 ;
    rep(i,0,n){
        auto it = ms.end();--it; pi ok = *it; ll mx = ok.first;
        ll rn = get<1>(v[i]); 
        ms.erase(ms.find({mp[rn],rn}));
        auto itr = ms.end();--itr; pi pok = *itr; ll pmx = pok.first;
        //cout<<mx<<" "<<pmx<<endl;
        if(mp[rn] != mx && mp[rn] + get<2>(v[i]) >= mx){
            ans++;
          
        }
        else if(mp[rn] == mx && mx == pmx && mp[rn] + get<2>(v[i]) > mx){
            ans++;
        }
        else if(mp[rn] == mx && mp[rn] + get<2>(v[i]) <= pmx){
                ans++;   
            
        }
        
        mp[rn] += get<2>(v[i]);
        ms.insert({mp[rn],rn});
    }
    cout<<ans<<endl;
}
```
**Problem 6:** http://www.usaco.org/index.php?page=viewproblem2&cpid=859
We just simulate the situation. For every iteration we delete exactly one cow. If there are no cows waiting, we take the next cow on the timeline, if there are cows waiting, we take the cow with the biggest senority.The sorting of times of cows and the seniority of cows are done using multisets. **Can you do this with pq?** Then we just erase that cow from whichever multiset and continue to do this until both multisets are empty meaning we went through every cow.


```cpp
void solve(){
    int n; cin>>n;
    multiset<tuple<int,int,int>> ms;
                           
    rep(i,0,n){
        int a,b; cin>>a>>b;
        ms.insert({a,i,b});
        
    }
    int time= 0;
    multiset<tuple<int,int,int>> wait;
    int ans = 0;
    while(ms.size() > 0 || wait.size() > 0){
        auto it = ms.begin();
        pi ok;
        if(wait.size()){
            
            it = wait.begin();
            tuple <int,int,int> pok = *it;
            ok.first = get<0>(pok);
            ok.second = get<2>(pok);
            ans = max(ans, time - get<1>(pok));
            time += ok.second;
            wait.erase(wait.begin());
        }
        else{
            it = ms.begin();
            tuple <int,int,int> pok = *it;
            ok.first = get<1>(pok);
            ok.second = get<2>(pok);
            time = get<0>(pok)+ok.second;
            ms.erase(pok);
        }
        vector <tuple<int,int,int>> del;
        for(auto itr = ms.begin();itr!= ms.end();itr++){
            tuple <int,int,int> pok = *itr;
            if(get<0>(pok) <= time){
                del.pb(pok);
                wait.insert({get<1>(pok),get<0>(pok),get<2>(pok)});
            }
            else{
                break;
            }
        }
        rep(i,0,del.size()){
            ms.erase(ms.find(del[i]));
        }

    }
    cout<<ans<<endl;
}
```
**Problem 7:** https://codeforces.com/gym/104002/problem/E
I really didn't have any clue how to solve this problem. I guess my greedy thinking was just wrong. I was thinking maybe we can first pick the max possible sum for k/2 numbers, then we see how to optimize so that it is possible. However, I did not know how to optimize so that we get the maximum sum such that everything works out. This was the wrong greedy approach, I just glimpsed through the editorial and actually, you just have to maintain the maximum **as you go along the array**. Lets always maintain x/2 values after we went through the first x numbers. For the i+1 and i+2 numbers they can either both be in the final or just 1 in the final( since we must have x/2 by the end of it). These are both possible if we just erase the smallest to make room. I still don't know how you can come up with this approach so I **got to review the editorial in more detail.** 
```cpp
void solve(){
    int n;
    cin>>n;
    vi v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    multiset <int> ms;
    rep(i,0,n/2){
        ms.insert(v[2*i]);
        if(*ms.begin() < v[2*i+1]){
            ms.erase(ms.begin());
            ms.insert(v[2*i+1]);
        }
    }
    int ans = 0;
    for(auto it = ms.begin();it != ms.end();it++){
        ans += *it;
    }
    cout<<ans<<endl;
}
```
**Problem 9:** https://codeforces.com/problemset/problem/1000/C
When I saw this problem I was like ya this is literally just prefix sums + coordinate compression like that lifeguard problem its literally the same exact thing so ya I just implemented that. **There is a simpler/cleaner code solution in "view user solutions" so I shall review**

```cpp
void solve(){
    int n;
    cin>>n;
    vector <ll> v;

    vector <pair<ll,ll>> add;
    rep(i,0,n){
        ll a,b;
        cin>>a>>b;
        add.pb({a,b});
        v.pb(a); v.pb(b); v.pb(b+1);
    }
    sort(v.begin(),v.end());
    v.erase(unique(v.begin(),v.end()), v.end());
 
    ll pre[v.size()+2]={0};
    rep(i,0,add.size()){
       
        pre[(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())]++;
        pre[(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())+1]--; 
        //cout<<(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())<<" "<<(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())+1<<endl;
    }
    rep(i,1,v.size()){
        pre[i] += pre[i-1]; 
    }

    ll ans[n+1]={0};
    rep(i,0,v.size()-1){
        ans[pre[i]] += v[i+1] - v[i];
        //cout<<pre[i]<<" "<<v[i+1]<<" "<<v[i]<<endl; 
    }
    rep(i,1,n+1){
        cout<<ans[i]<<" ";
    }
    cout<<endl;
}


```


**Problem 12:** https://codeforces.com/problemset/problem/1805/F1
This can be done in O(n^2)
I dont really get how we can implement F in O(n), imma check hint
