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
/*

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
find the minimum time lost. The answer is time of all lifeguards - min.
```cpp




```


