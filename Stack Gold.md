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

**Problem 1:** https://cses.fi/problemset/task/1142/
This is just a easier version of Largest Rectangle Histogram, using the same technique as nearest left smallest element problem. Lets calculate for every bar, the largest rectangle we can form if teh rectangle is bounded by that bar, meaning its highest is v_i. We can calculate the nearest left smaller element and nearest right smaller element. These are the left and right bounds which bounds the rectangle since we cannot go past that with height v_i. We then find the max of all these rectangles.
```cpp
void solve(){
    int n;cin>>n;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u;
        v.pb(u);
    }
    stack <pi> st; // {value, position}
    ll ans = 0;
    ll left[n];
    rep(i,0,n){
        while(st.size() > 0 && st.top().first >= v[i]){
            // if element is not smaller than curr
            st.pop();
        }
        if(st.size() == 0){ // curr element is smallest
            left[i] = 0;
        }
        else{
            left[i] = st.top().second; // top element is last smallest
        }
        st.push({v[i],i+1}); // add curr to stack
    }
    // right to left
    ll right[n];
    //st.clear()
    while(st.size()){
        st.pop();
    }
    for(int i = n-1;i>=0;i--){
        while(st.size() > 0 && st.top().first >= v[i]){
            // if element is not smaller than curr
            st.pop();
        }
        if(st.size() == 0){ // curr element is smallest
            right[i] = n+1;
        }
        else{
            right[i] = st.top().second; // top element is last smallest
        }
        st.push({v[i],i+1}); // add curr to stack
    }
    rep(i,0,n){
        ans = max(ans, (right[i]-1-left[i]) * v[i]);
        // right bound = right[i]-1
        // left bound = left[i] + 1
        // length = right - left + 1 
         
    }
    cout<<ans<<endl;
}
```
