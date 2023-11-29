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
Ordered(sorted) Sets and Maps supports
- lower_bound(x): returns iterator to the smallest value/key **greater or equal** to x
- upper_bound(x): returns iterator to the smallest value/key **greater** than x

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



