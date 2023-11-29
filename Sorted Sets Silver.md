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
Simulate the scenario with a multiset or priority queue

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

