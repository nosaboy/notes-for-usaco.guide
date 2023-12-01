### Usage
- Sort with custom conditions in a non-default order( not the default sort() function that sorts smallest to largest)
- Compress larger range values to a smaller range( using sorting)
 
#### Default sort
##### Vectors
```cpp
vector <int> v = {3,1,2};
sort(v.begin(),v.end()); // {1,2,3}
sort(v.rbegin(),v.rend()); // {3,2,1}
/*
for vectors with tuples, it will sort from the smallest
to largest prioritizing earlier elements( has more weighting):
sort by first, then second if first is equal, etc...
*/
vector <pair<int,int>> v;
v.push_back({1,2}); v.push_back({2,3}); v.push_back({1,3});
sort(v.begin(),v.end()); // {{1,2},{1,3},{2,3}}
// using array<> instead of tuple
vector<array<int,3>> v; // or vector <vector<int>> v;
v.push_back({1,2,3});
cout<<v[0][0]<<" "<<v[0][1]<<" "<<v[0][2]<<endl; // 1 2 3
```
##### Arrays
```cpp
int n = 3; // array size
int a[] = {3,1,2};
sort(a,a+n); // {1,2,3}
```
##### String
```cpp
string s = "nosa";
sort(s.begin(),s.end()); // "anos"
```

### Classes
A user-defined data type that represents the structure of what we want to sort.
When using struck, everything is public by default
```cpp
struct osa{
  int a,b,c;
}

vector <osa> v;
v.push_back({1,2,3});
cout<<v[0].a<<" "<<v[0].b<<" "<<v[0].c<<endl; // 1 2 3 
```

### Comparators
A function that compares **two** objects at a time. 
- If object x goes **before**(left of) object y in sort, return **true**
- If object x goes **after**(right of) object y in sort, return **false**
- If object x = object y, return **false** or else WA or RTE

The function must also satisfy:
- reverse: if compare(x,y) = true, then compare(y,x) = false
- transitive: if compare(x,y) = true and compare(y,z) = true, then compare(x,z) = true;

```cpp
bool cmp(const osa &x, const osa &y){
  return x.a < y.a; // sort by first
//return x.a > y.a; // decreasing
}

...
vector <osa> v;
sort(v.begin(),v.end(),cmp);

```
#### Two Criteria
When we want to sort first by $a$, then by $b$ if $a$ values are equal
```cpp
bool cmp(const osa &x, const osa &y){
  if(x.a == x.b){ //secondary condition if a values are the same
      return x.b < y.b; // sort by b
  }
  return x.a < y.a;
}
```
Sort a string primarily by length and then by alphabetical order
```cpp
bool cmp(string a, string b){
  if(a.size() == b.size()){
    return a < b;
  }
  return a.size() < b.size();
}
```

### Coordinate Compression
mapping each value to a smaller value based on its position in a sorted array.
We do this when we have large values, but **we only care about their relative order**
To store a large range of values, we will MLE so we must use coordinate compression.
```cpp
/*
unique function: removes every consecutive group of elements that have the same value except the first value in a given range
unique(first, last) removes all duplicates including first but not including last
it will return the iterator to the element after the last element that was not removed
It will just set the erased values as "?" unknown values at the end of the vector, so we must erase them
We must use the erase(first, last) which removes all elements including first but not including last.

Thus, to have all unique values in a vector, we must sort the vector then erase duplicates:
*/

sort(v.begin(),v.end());
v.erase(v.unique(v.begin(),v.end()) , v.end());

/*
We then get the index of every number by finding lower_bound(v[i]) and finding that index
*/
int getccindex(int x){
 return lower_bound(v.begin(),v.end(), x) - v.begin(); // returns index of x in v
}
```

**Example 1:** https://codeforces.com/gym/102951/problem/D
This is just prefix sums by summing up ranges but with larger constraints on the number line. 
If we just prefix sum from 1 to 10^9, it will MLE.
We know that there must be at most 4 * 10^5 distinct points that we can check since thats the max possible distinct ranges.
We can then first get all points then coordinate compress them.

Yup, after 20Wa, I might just keep myself very safe tn yk what I mean. The problem was that I put n instead of q so I didn't load up the rest of the queries. Wasted an hour cool!

```cpp
void solve(){
    int n,q;
    cin>>n>>q;
    vector <tuple<int,int,int>> add;
    vi v;
    rep(i,0,n){
        int a,b,c;
        cin>>a>>b>>c;
        add.pb({a,b,c});
        v.pb(a); v.pb(b);
    }
    vector <pi> query;
    rep(i,0,q){
        int a,b;
        cin>>a>>b;
        query.pb({a,b});
        v.pb(a); v.pb(b);
    }
    sort(v.begin(),v.end());
    v.erase(unique(v.begin(),v.end()), v.end());
    map <ll,ll> mp; // store initial value
    rep(i,0,v.size()){
        mp[i] = v[i];
    }
    ll pre[v.size()+2]={0}; // prefix sum shift by 1 
    rep(i,0,add.size()){
       
        pre[(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())+1] += get<2>(add[i]);
        pre[(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())+1] -= get<2>(add[i]); // we dont include r, so we end it
    }
    //precomputing prefix sum
    // we let pre[i] be the sum of mp[i]....mp[i+1]
    rep(i,1,v.size()+2){
        pre[i] += pre[i-1];
    }
    rep(i,1,v.size()){
        pre[i] *= mp[i] - mp[i-1]; // difference in length = r - l since we dont include mp[i+1]
        // everything in between mp[i] and mp[i+1] is the same = pre[i]
    }
    rep(i,1,v.size()+2){
        pre[i] += pre[i-1]; // prefix sum one again to get the sum of all values in a range
        // remember the previous prefix sum was to get the sum of ranges so that we know the values for each i
    }
    rep(i,0,query.size()){
        cout<<pre[(lower_bound(v.begin(),v.end(), query[i].second) - v.begin())] - pre[(lower_bound(v.begin(),v.end(), query[i].first) - v.begin())]<<endl;
        
    }
}
```



### Problems
**Problem 1:** https://cses.fi/problemset/task/1619
Use coordinate compression, then this just turns into a classic prefix sum problem where you have to sum up ranges.
```cpp
void solve(){
    int n;
    cin>>n;
    vi v;

    vector <pi> add;
    rep(i,0,n){
        int a,b;
        cin>>a>>b;
        add.pb({a,b});
        v.pb(a); v.pb(b);
    }
    sort(v.begin(),v.end());
    v.erase(unique(v.begin(),v.end()), v.end());
 
    int pre[v.size()+2]={0}; // prefix sum shift by 1 
    rep(i,0,add.size()){
       
        pre[(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())+1] ++;
        pre[(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())+1]--; // customer left
    }
    int ans = 0;
    rep(i,1,v.size()+2){
        pre[i] += pre[i-1]; // how many customers at time i
        ans = max(ans, pre[i]);
    }
    cout<<ans<<endl;
}
```

**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=786
This is just the same idea as problem 1 where you have to coordinate compress then sum up the ranges. However, there is one more step.
If any lifeguard is fired, the time they will lose is the number of 1s in the lifeguards interval, since they will be 0 after lifeguard is fired.
Thus, we add one more step where we do a prefix sum to count the number of 1s for any range, then brute force loop through each lifeguard and
find the minimum time lost. The answer is time of all lifeguards - min. Still insta mindsolve.
```cpp
void solve(){
    int n;
    cin>>n;
    vi v;

    vector <pi> add;
    rep(i,0,n){
        int a,b;
        cin>>a>>b;
        add.pb({a,b});
        v.pb(a); v.pb(b);
    }
    sort(v.begin(),v.end());
    v.erase(unique(v.begin(),v.end()), v.end());
 
    int pre[v.size()+2]={0}; // prefix sum shift by 1 
    rep(i,0,add.size()){
       
        pre[(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())+1] ++;
        pre[(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())+1]--; // customer left
    }
    int ans = 0;
    int total = 0; // total time
    rep(i,1,v.size()){
        pre[i] += pre[i-1]; // how many customers at time i
        if(pre[i] > 0){
            total+=v[i] - v[i-1];
        }
    }
    int ones[v.size()+2]={0}; // number of ones 
    rep(i,1,v.size()+1){
        if(pre[i] == 1){
            ones[i]+=v[i] - v[i-1];
        }
        ones[i]+=ones[i-1];
    }
    int mn = 1000000005;
    rep(i,0,add.size()){
        mn = min(mn, ones[(lower_bound(v.begin(),v.end(), get<1>(add[i])) - v.begin())] - ones[(lower_bound(v.begin(),v.end(), get<0>(add[i])) - v.begin())]);
    }
    cout<<total- mn<<endl;
}

```

**Problem 3:** http://www.usaco.org/index.php?page=viewproblem2&cpid=787
This problem was greedy. It is always better to sell a cow that produces smaller milk per day. If a cow's milk can sell some interval, it is most optimal that a cow that produces a larger amount of milk sells its milk at a higher interval. We precompute the interval each cow sells( sort cow by most produced milk per day, then that milk must sell as much as possible of the maximum price). So the first cow sells as much milk as possible from the largest buyer, etc. 
We then see if it's more optimal to sell the cow rather than sell its milk in the interval. Overall, the hard part is getting the correct greedy to solve this.
```cpp
void solve(){
    ll n,m,r;
    cin>>n>>m>>r;
    vector<ll> v; vector <pair<ll,ll>> osa; vector<ll> a;
    rep(i,0,n){
        ll u; cin>>u;
        v.pb(u);
    }
    sort(v.begin(),v.end());
    
    rep(i,0,m){
        ll x,y;
        cin>>x>>y;
        osa.pb({y,x});
    }
    sort(osa.rbegin(),osa.rend());
    ll pre[n]={0};
    ll it = 0; 
    for(int i = n-1;i>=0;i--){
        ll cnt = v[i];
        ll nosa = 0;
        //cout<<cnt<<endl;
        while(it < m && cnt > 0){
            ll lol = 0;
            ll ok = min(osa[it].second , cnt);
            if(osa[it].second <= cnt){
                lol=1;
            }
            else{
                osa[it].second -= cnt;
            }
            
            nosa += ok * osa[it].first;
            cnt -= ok;
            it+=lol;
            
        }
        pre[i] = nosa;
    }
    rep(i,0,r){
        ll u; cin>>u;
        a.pb(u);
    }
    sort(a.rbegin(),a.rend());
  
    ll itr = 0;
    ll ans = 0;
    rep(i,0,v.size()){

        if(itr < r && pre[i] < a[itr]){
            ans += a[itr];
            itr++;           
        }
        else{            
            ans += pre[i];           
        }
        //cout<<v[i]<<" "<<pre[i]<<" "<<ans<<endl;
    }
    cout<<ans<<endl;  
}

```


**Problem 4:** http://www.usaco.org/index.php?page=viewproblem2&cpid=896
A mountain with peak (x,y) covers another mountain with peak (a,b) on the right of it( with x <= a) if x + y >= a+b. Proof: Lets imagine we erase the x coordinates until we reach x as 0 so we have (0,y) and (a-x,b) so we want to prove y1 >= a + b - x1. If a + b - x1 < y1 it must mean that it is within the region of y = -x + y1 -> math coordinate geo or smth. Thus, we can just check for every coordinate to the right and delete that if its sum satisfies the inequality. We then reverse the x coordinates( and not the y coordinate) so we can check the other way(right to left for the other side of the mountain) for every coordinate remaining. We then print out the remaining coordinates after checking both ways.
**There is a easier solution with less lines of code, please review**
```cpp
void solve(){
    ll n; cin>>n;
    multiset <pair<ll,ll>> left;
    vector <pair<ll,ll>> v;
    rep(i,0,n){
        ll x,y; cin>>x>>y;
        left.insert({{x+y},y}); v.pb({x,y});
    }
    sort(v.begin(),v.end());
    multiset <pair<ll,ll>> right;
    rep(i,0,n){
        ll x = v[i].first; ll y = v[i].second;
        if(left.find({x+y,y}) != left.end()){
            while(left.size()>0){
                pair<ll,ll> ok = *left.begin();
                if(ok.first - x <= y){
                    left.erase(left.find(ok));
                }
                else{
                    break;
                }
            }
            //cout<<x<<" "<<y<<endl;
            x = 1000000005 - x; 
            right.insert({x+y,y});
        }
        
    }
    for(auto it = right.begin();it != right.end();it++){
        pi ok = *it;
        //cout<<ok.first<<" "<<ok.second<<endl;
    }
    sort(v.rbegin(),v.rend());
    //cout<<endl;
    ll ans = 0;
    rep(i,0,n){
        ll x = 1000000005 -v[i].first ; ll y = v[i].second;
        
        if(right.find({x+y,y}) != right.end()){
            
            while(right.size()>0){
                pair<ll,ll> ok = *right.begin();
                //cout<<ok.first<<" "<<ok.first - x<<" "<<y<<endl;
                if(ok.first - x <= y){
                    right.erase(right.find(ok));
                }
                else{
                    break;
                }
            }
            ans++;
        }
        
    }
    cout<<ans<<endl;
}
```




**Problem 6:** http://www.usaco.org/index.php?page=viewproblem2&cpid=645
We know that given a range of points, the area of a box that incloses all points is (max(x) - min(x)) * (max(y) - min(y)).

We can only split the field into two non-overlapping boxes if both of them are vertical that covers all y values in a certain range of x, or both horizontal where they cover all x values in a certain range of y values.
We can brute force all of these boxes.
```cpp
bool cmp(const pi &x, const pi &y){ // horizonal case sorts by second first
    if(x.second == y.second){
        return x.first < y.first;
    }
    return x.second < y.second;
}

void solve(){
	int n;cin>>n;
    vector <pi> v;
    multiset <int> firx;
    multiset <int> firy;
    multiset <int> secx;
    multiset <int> secy;
    int mxx = 0; int mnx = 1000000005; int mxy = 0; int mny = 1000000005;
    rep(i,0,n){
        int x,y;
        cin>>x>>y;
        v.pb({x,y});
        secx.insert(x);
        secy.insert(y);
        mxx = max(mxx, x); mxy = max(mxy, y);
        mnx = min(mnx, x); mny = min(mny, y);
    }

    sort(v.begin(),v.end());
    // vertical case
    int ans = (mxx-mnx) * (mxy - mny);
    int one = (mxx-mnx) * (mxy - mny); // orginial value with only 1 field
    rep(i,0,n){
        // putting the next cow in the first field
        firx.insert(v[i].first);
        firy.insert(v[i].second);
        secx.erase(secx.find(v[i].first));
        secy.erase(secy.find(v[i].second));
        if(i == n-1 || v[i].first != v[i+1].first){ //we construct a box
            
            if(firx.size() > 0 && secx.size() > 0){
                // calculating min max x values of both fields
                auto firleftx = firx.begin();
                auto firrightx = firx.end(); firrightx--;
                auto firlefty = firy.begin();
                auto firrighty = firy.end(); firrighty--;

                // calculating min max y values of both fields
                auto secleftx = secx.begin();
                auto secrightx = secx.end(); secrightx--;
                auto seclefty = secy.begin();
                auto secrighty = secy.end(); secrighty--;
                ans = min(ans, (*firrightx - *firleftx) * (*firrighty - *firlefty) + (*secrightx - *secleftx) * (*secrighty - *seclefty));
                

            }
            
        }
        
      
    }
    // horizontal case
    rep(i,0,n){

        secx.insert(v[i].first);
        secy.insert(v[i].second);
     
    }
    firx.clear(); firy.clear();
    sort(v.begin(),v.end(),cmp);
    rep(i,0,n){
        // putting the next cow in the first field
        firx.insert(v[i].first);
        firy.insert(v[i].second);
        secx.erase(secx.find(v[i].first));
        secy.erase(secy.find(v[i].second));
        if(i == n-1 || v[i].second != v[i+1].second){ //we construct a box
            
            if(firx.size() > 0 && secx.size() > 0){
                // calculating min max x values of both fields
                auto firleftx = firx.begin();
                auto firrightx = firx.end(); firrightx--;
                auto firlefty = firy.begin();
                auto firrighty = firy.end(); firrighty--;

                // calculating min max y values of both fields
                auto secleftx = secx.begin();
                auto secrightx = secx.end(); secrightx--;
                auto seclefty = secy.begin();
                auto secrighty = secy.end(); secrighty--;
                ans = min(ans, (*firrightx - *firleftx) * (*firrighty - *firlefty) + (*secrightx - *secleftx) * (*secrighty - *seclefty));
               

            }
            
        }
        
      
    }
    cout<<one-ans<<endl;
}   
```
