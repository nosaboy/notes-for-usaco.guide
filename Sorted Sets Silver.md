#### Unordered Sets
A set but elements are in a random order instead of sorted so we don't know the next highest value. Works by hashing, meaning **high constant factor**: 
assigning code to specific value.
- Every operation is O(1) instead of set which is O(logn)
- Except for .erase() and .clear() which is O(n)

```cpp
unordered_set<int> st;
st.insert(1); // [1]
st.insert(2); // [1, 2] in arbitrary order
```
#### Unordered Maps
Map but it is not sorted by key(mp[key] = value) so we don't know the next highest key. Works by hashing, meaning **high constant factor**.
- Every operation is O(1) instead of O(logn)
- Supports insert: mp[key] = value, erase: mp.erase(key), count: mp.count(key)

**Can use unordered in USACO. Don't use unordered in codeforces**

#### Iterators
Ordered(sorted) Sets and Maps supports
- lower_bound()
- upper_bound()
