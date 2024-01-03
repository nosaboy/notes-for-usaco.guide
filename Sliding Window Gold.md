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

