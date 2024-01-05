## Usage
- Assigns unique code to an object: allows insertion, deletion, searching in O(1)
  - Elements are **not sorted**


Normal unordered sets & maps cannot hash pairs or vectors. When tried, long lines of error will appear.
### Custom Hashing
Unordered map converts a key using a custom hash code using std::hash< Key >.

```cpp
struct hash_name { // declares a struct for hash related functions
    size_t operator()(const Name &name ) const // defines operatior function
    {
        return hash<string>()(name.first) ^ hash<string>()(name.second); // calculates hash value for Name
    }
};
```


### Resizing
