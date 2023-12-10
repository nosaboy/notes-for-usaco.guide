#### Operations:
```cpp
stack <int> st;
st.pop() // erases top element
st.push(x) // adds x to the top
int osa = st.top() // access the top element 
```

#### Bracket Matching Problem: Given bracket sequence, compute for every open bracket its corresponding closed bracket.
An opening bracket will always match up with the closing bracket that was put in last. Then they will cancel. Thus we can maintain a stack of opening bracket indexes. And for each closing bracket we take the index at the top of the stack(last added opening bracket) and delete it. 

### Monotonic Stack
**Example 1:** https://cses.fi/problemset/task/1645
For every element, the first smaller element to its left can be solved by maintaining a stack. Let the stack denote elements to the left in decreasing order from top to bottom. We go through the stack, and erase any element that is bigger than current element. This is because for future references, we will not use any of these elements since it is always more optimal to use current element in the future(since its position is bigger than all previous). We thus pop until we reach a smaller element. Then the answer for current element is that element. We must not forget to push curr element to the end after.
**NOTE: Each element was added and erased EXACTLY ONCE, so complexity is O(n)**
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;
        v.pb(u);
    }
    stack <pi> st; // {value, position}
    int ans[n];
    rep(i,0,n){
        while(st.size() > 0 && st.top().first >= v[i]){
            // if element is not smaller than curr
            st.pop();
        }
        if(st.size() == 0){ // curr element is smallest
            ans[i] = 0;
        }
        else{
            ans[i] = st.top().second; // top element is last smallest
        }
        st.push({v[i],i+1}); // add curr to stack
    }
    rep(i,0,n){
        cout<<ans[i]<<" ";
    }
}

```

