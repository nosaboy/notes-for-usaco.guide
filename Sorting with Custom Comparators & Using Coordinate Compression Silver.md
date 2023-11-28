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
Sort a string primary by length and then by alphabetical order
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

**Example 1:** https://codeforces.com/gym/102951/problem/D
This is just prefix sums by summing up ranges but with larger constraints on the number line. 
