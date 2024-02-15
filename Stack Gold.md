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

**Problem 2:**
We realize that for every number, we can find the maximum range such that this is the smallest number. We can do this by calculating the nearest smallest left element and nearest smallest right element. The range(x+1...y-1) = l_i will be our maximum range for this element. The length is calculate similar to the previous problem 1. We thus know that for every length from 1 to l_i, we can have this value. as our min. Thus we just find the max for each range by storing the max for each length, then the max of ;length x is just the max value of all lengths x to n.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;
        v.pb(u);
    }
    stack <pi> st; // {value, position}
    int left[n];
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
    int right[n];
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
    int ans[n+1]={0};
    rep(i,0,n){
        // right[i]-1-left[i] = the max length that has v[i] as smallest element
        ans[(right[i]-1-left[i])] = max(ans[(right[i]-1-left[i])], v[i]);
         
    }
    for(int i = n-1;i>=1;i--){
        // max length = max(mx[x...n])
        ans[i] = max(ans[i], ans[i+1]);
    }
    rep(i,1,n+1){
        cout<<ans[i]<<" ";
    }
    
}
```

**Problem 6:** https://cses.fi/problemset/task/1147
- Calculate maximum rectangle histogram(Problem 1:CSES Advertisement) using 1 stack instead of 2.
- Find the largest rectangle area that doesnt contain some character in O(nm)
We can use the maximum rectangle histogram approach, **but in 2d**. 
We fix the bottom of the rectangle(loop for every row), then apply the maximum rectangle histogram in O(m). Time is O(nm).
We use only 1 monotonic stack to calculate the maximum rectangle histogram. We know that the stack of heights is sorted in increasing order. Thus we say for every previous position, what is the maximum rectangle that can be formed that has right side at the i-1th index and left side at stack.top(). Since stack is sorted, it must have height stack.top(). We do this until the stack.top() < ith height, in which stack.top() will no longer be the smallest height. We then push the leftmost position such that the ith height is the smallest to the top of the stack. This way when we use the ith height as our left side we can get the maximum length. 

```cpp
void solve(){
    int n,m; cin>>n>>m;
	stack<int> nxt[m];
	int g[n][m]; 
    rep(j,0,m){
        nxt[j].push(-1); // border 
    }
    rep(i,0,n){
        string s;cin>>s;
        rep(j,0,m){
            g[i][j] = s[j];
			if(g[i][j] == '*'){
                nxt[j].push(i); // sort tree position top to bottom
			}
        }

    }
    
    int ans = 0;
    for(int i = n-1;i>=0;i--){ // for every row
        stack <pi> st;
        st.push({-1,-1}); // border account for RTE(stack has no elements)
        rep(j,0,m){
            while(nxt[j].top() > i){ // tree is below curr row on grid
                nxt[j].pop(); // pop this tree since it doesn't affect us
            }
            // (i - nxt[j].top()) = height of histogram(how much up until we reach tree or border)
            int start = j;
            while(st.size() > 0 && st.top().first > i - nxt[j].top()){
                start = st.top().second;
                ans = max(ans, (st.top().first) * (j - st.top().second)); // st.top().first smallest height,
                // the rectangle has height st.top().first and length: [st.top().second...j-1]
                st.pop();
            }
            // (j - st.top().second) = length of histogram(how much to the left until we reach smaller
            // histogram or border)
            st.push({(i - nxt[j].top()), start}); // push the leftmost position such that curr height is smallest
        }
        while(st.size() > 0){

            ans = max(ans, (st.top().first) * (m - st.top().second));
            st.pop();
        }
    }
    cout<<ans<<endl;
    
    
}
```

**Problem 11:** https://codeforces.com/contest/1482/problem/E

Essentially, we want to divide an array of numbers into segements such that the sum of the b_i(where i is the min of all h_i in that segment) is maximum. 

If we can loop through all rectangles that have h_i as the shortest for all curr i, we can run dp. This process will probably be similar to largest histogram.

The dp will go as such, $dp[i]$ will be the maximum sum for subarray 1...i. To calculate the dp, we look at the last element in array ${h[i],a[i]}.$ There are two cases. Either this is encompassed by some prev number($h[j]<h[i])$, or this is the minimum height of the last photo.
For the first case, we will just store the max prev dp value with $h[j]<h[i]$ in the stack. Thne, the ans will be just that.
For the second case, the segment start must be from $[prev_pos+1...i]$, thus we will get the max dp from 1 element previous, then add $a[i]$ to this. We can query this using maximum segtree. If we go anywhere below prev_pos, our current photo encompasses and element whose h_i is smaller than it so it would fall under first case.


Reflection: This was pretty free, **pls review O(n) solution without segtree.**

```cpp
void solve(){
    
    ll n;cin>>n;
    ll h[n];
    ll a[n];
    rep(i,0,n){
        cin>>h[i];
    }
    rep(i,0,n){
        cin>>a[i];
    }
    stack <tuple<ll,ll,ll>> st;
    ll dp[n+1]={0}; // dp[0]=0 base case
    rep(i,1,n+1){ // everything else is -inf
        dp[i]=-1000000000000000000LL;
    }
    segtree s; // query MAXIMUM segtree
    s.init(n);
    s.set(0,dp[0]);
    st.push({0,0,-1000000000000000000}); // base case to prevent empty stack
    rep(i,1,n+1){
        while(!st.empty() && get<0>(st.top()) > h[i-1]){
            st.pop();
        }
        ll pos = get<1>(st.top()); // prev pos
        ll prev = get<2>(st.top());
        dp[i] = max(prev, s.dfs(pos,i)+a[i-1]); // must be either prev dp encompass or max of dp from [prev...i) + curr a[i]
        s.set(i,dp[i]);
        st.push({h[i-1], i, max(dp[i], prev)});
    }
    cout<<dp[n]<<endl;
    
}
```
