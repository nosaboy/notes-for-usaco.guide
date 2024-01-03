A constant sized subarray(window) that moves(slides) through the array. We want to compute some information within each window.
Sliding Window Problems requires us to transition between windows and calculate how that affects the answer. Meaning we shift window by 1 by l++ and r++: subtract the left element and add the next right element.
**Example 1:** https://leetcode.com/problems/sliding-window-maximum/

Return the maximum value for each sliding window

We want to know how transitioning from 1 window to the next will affect current answer(max element). For each transition, we delete the previous element at left border(l) and add the new element on the right(r+1). We then calculate max of all elements. 
We can use multiset to do these 3 operations:
```cpp
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        multiset <int> ms;
        vector <int> ans;
        for(int i = 0;i<k;i++){
            ms.insert(nums[i]);
        }   
        
        int l = 0;
        for(int i = k-1;i<nums.size();i++){
            auto it = ms.end(); --it; // get max
            ans.push_back(*it);
            ms.erase(ms.find(nums[l])); // delete last left element
            l++;
            if(i+1<nums.size()){ // if possible
                ms.insert(nums[i+1]); // right
            }
        }
        return ans;
    }
};
```

**Example 2:** https://cses.fi/problemset/task/1141

This is a dynamic sliding window problem( just two pointers), meaning the window size changes. We fix the left bound. Then we get the maximum possible right bound. To do this, we maintain a map of elements in the window. 
The left pointer transition is losing the leftmost value. Since all values in the window are distinct, we only need to say $map[value]=0$ meaning that the window no longer contains this value.
The right pointer transition is adding a new value. We check if its possible to add this value using the map and if it is we set $map[value]=1$ meaning the value is in the window. We add as much as possible until we reach the first value that we cant add cause its already in the window.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    int ans = 0;
    int r = 0;
    map <int,int> mp; // is value in current window
    rep(i,0,n){
        while(r < n && mp[v[r]]==0){
            mp[v[r]]=1;
            r++;
        }
        ans = max(ans, r-i); // length, since r is pointing to the value we cant add
        mp[v[i]]=0; // set back to 0 since we're moving window
    }
    cout<<ans<<endl;
}   

```
