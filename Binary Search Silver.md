## ITMO Edu 1 and 2
**Given sorted array a and k queries, for each query find element x in the array using binary search in O(logn).**
Check middle element:
If a_m > x, it is to the right of a_m, we shrink range to only right of a_m, and repeat process. 
If a_m = x, we have found element.
If a_m < x, it is to the left of a_m, we shrink range to only left of a_m, and repeat process.
```cpp
void solve(){
    int n,k; cin>>n>>k; vi v;
	rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    while(k--){
        int x; cin>>x;
        int l = 0; int r = n-1; // left and right bounds
        bool ans = 0;
        while(l <= r){ // the two pointers can be equal, meaning we are picking that element
            int mid = (l+r) / 2;
            if(v[mid] == x){ // we have found element
                ans = 1;
                break;
            }
            else if(v[mid] < x){ // mid.....x
                l = mid + 1;
                
            }
            else{ // x.....mid
                r = mid - 1;
            }
        }
        if(ans){
            cout<<"YES\n";
        }
        else{
            cout<<"NO\n";
        }
    }
    
}
```
**Given sorted array a and k queries, for each query find CLOSEST element TO x in the array using binary search in O(logn).**

We can either find x, find value to right of x, or find value to left of x. So the value will be min i such that a_i >= x(element to right of x) or max i such that a_i <= x(element to left of x).

Maintain two pointers l,r such that a_l < x and a_r >= x is always satisfied. Add negative inf and positive inf on both sides, and use that as starting values of l and r, so let index of l = -1(-inf) and r = n(+inf).
We calculate mid = (l+r)/2, then if mid < x, we set l = mid which satisfies a_l < x since all numbers smaller than mid will also satisfy a_l < x. Similarily if mid >= x we set r = mid since all bigger elements will also satisfy r = mid, so both conditions are maintained.
The two conditions cannot be satisfied by a single element, so we do while(l+1 < r) since we want to loop until the two converge, meaning if we have l = l and r = l+1 we know that we have already found the answers so we break. In the end, the left element will be v_l and right element will be v_r. We can then find ans = whichever is minimal: ans - v_l or v_r - ans.
If all elements smaller than x and we are looking for 

**Maximum index such that value smaller or equal to than given x**
```cpp
void solve(){
    int n,k; cin>>n>>k; vi v;
	rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    while(k--){
        int x; cin>>x;
        int l = -1; int r = n; // left and right bounds 
        while(l + 1 < r){ // l represents smaller or equal to x, r represent bigger than x
            // a[l] <= x, a[r] > x, so two pointers cannot be the same
            int mid = (l+r) / 2;
            if(v[mid] <= x){ // mid.....x
                l = mid; // mid satisfies condition of a[l] <= x, so everything smaller will also satisfy
                
            }
            else{ // x.....mid
                r = mid;
            }
        }
        cout<<l + 1<<endl; // the answer is 1 indexed instead of 0 indexed
    }
    
}

```

**Minimum index such that value bigger or equal to than given x**
```cpp
void solve(){
    int n,k; cin>>n>>k; vi v;
	rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    while(k--){
        int x; cin>>x;
        int l = -1; int r = n; // left and right bounds 
        while(l + 1 < r){ // l represents smaller or equal to x, r represent bigger than x
            // a[l] <= x, a[r] > x, so two pointers cannot be the same
            int mid = (l+r) / 2;
            if(v[mid] <= x){ // mid.....x
                l = mid; // mid satisfies condition of a[l] <= x, so everything smaller will also satisfy
                
            }
            else{ // x.....mid
                r = mid;
            }
        }

        cout<<l + 1<<endl; // the answer is 1 indexed instead of 0 indexed
    }
    
}

```

To check if element is equal to x using this implementation, we know that if there is such element it must be at pointer r since $a[r] >= x$ and smallest r must be equal to x if it exists. So we just add at the end of binary search:
```cpp
if(v[r] == x){
  cout<<"YES\n";
}
else{
  cout<<"NO\n";
}
```

**Given array a and query that have two values l,r, determine number of integers inside range l...r inclusive.**
We can find the left bound for all numbers >= l and right bound for all numbers <= r using binary search methods above. Then ans = right - left + 1.
```cpp
void solve(){
    int n; cin>>n; vi v;
	rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    sort(v.begin(),v.end());
    int k; cin>>k;
    while(k--){
        int x,y; cin>>x>>y;
        int l = -1; int r = n; 
        int left = 0;
        while(l + 1 < r){ // until the two pointers converge
            int mid = (l+r) / 2;
            if(v[mid] < x){ // x.....mid
                l = mid; // all values smaller will satisfy v[l] < mid
                
            }
            else{ // mid.....x
                r = mid;
            }
        }
        left = l;
        l = -1; r = n;
        while(l + 1 < r){ 
 
            int mid = (l+r) / 2;
            if(v[mid] > y){ // y.....mid
                r = mid; // all values bigger will satisfy v[r] > mid
                
            }
            else{ // mid.....y
                l = mid;
            }
        }
        cout<<(r-1) - (left + 1) + 1<<endl; // left is left + 1 while right is r - 1

    }
    
}
```

### Binary Search for Answer
We want to find some "good" value such that the function good() is monotonic meaning if x is good then x + 1 is good or if x is good then x - 1 is good. We usually have to find the first good value overall values( the first time where bad turns into good on number line: 000000...011111...).

Use two pointers l and r where:
- left pointer always points to some bad integer
- right pointer always points to some good integer
and we want to make the as close as possible by peeking the middle.

```cpp
while(l + 1 < r){
        int m = (l+r)/2;
        if(good(m)){
            r = m; // we shrink r = m since m is good
        }
        else{
            l = m; // set l = m since m is bad as well
        }
}
```

We can do this for any problem where good() is monotonic function and we are asked the max/min value that is good.
Sometimes we don't know a specific r starting value that works. We can limit our search by only checking specific value such as powers of 2s. We can brute force over all 2^k and find a value that satisifies good() and set the as our starting r value.

**Given n rectangles with h * l, find smallest square that all n rectangles can fit in it.**

We can use binary search. The function good() to check if all rectangles fit in a square side length x is floor(x/l) * floor(x/r) >= n? THis is because we can fit floor(x/l) rectangles at for length and floor(x/r) rectangles for width.
If for some x good(x) = true, we know that all values above x would also be true so the monotonic function looks like: 000...00111...111.
Thus, we set that l = 0 and r = 1 is satisifed and find smallest r.

```cpp
void solve(){
	
    ll w,h,n; cin>>w>>h>>n;
    ll l = 0;
    
    ll cnt = 1;
    ll r;
    rep(i,0,60){ // find some smallest good value 2^k
        if((cnt/w) * (cnt/h) >= n){
            r = cnt;
            break;
        }
        cnt *= 2;
    }
    
    while(l + 1 < r){
        ll m = (l+r)/2;
        if((m/w) * (m/h) >= n){ // m is good
            // If we just put r = 10^18, this will int overflow 
            // since if we peek m = 10^18 we have 10^32 multiplication
            
            r = m;
        }
        else{
            l = m;
        }
    }
    cout<<r<<endl; // the smallest good value
   
}
```

**Given length of n ropes, divide some ropes into k ropes of the same length, what is the maximum length of this length where it is a real number**

This is hard to do greedily, but we can just simulate/check using binary search since if some rope length is satisfied, then all rope lengths below it is also satisfied. Thus, the monotonic function is 111...11000...000. Thus, we always set l = 1 and r = 0 and solve for max l. To find good() for a given x, we just greedily cut the max amount from each rope, so we have floor(length/x) for each rope. If the sum >= k, we know we can always form k ropes. 

**Note: This problem deals with REAL NUMBERS** so we must use binary search on real numbers since the two pointers can be infinitely close together. We could do while(r - l > eps) for some small eps.
A problem that leads to TLE is if the eps is too small, so when numbers are stored in floating point to some decimal and are taken the average it might remain the same number, so the average of 0.000...01 and 0.000...02 might be 0.000...01 since it does not store many digits after decimal, which causes the while loop to run infinitely. One way to fix TLE is to **set a max number of operations that we will loop through** and we break after we reached that many operations.
Instead of while loop we can use for loop which fixes the number of operations. Given that l and r are both 10^9 and we want 10^-9 accuracy, it will take log(10^18) = 60 something operations. Thus we can just put 100 to be safe. We can check the exact number of operations we need by running binary search and printing l and r values, then finding when does double reach accuracy of eps = 10^-6 or smth.

```cpp
void solve(){
	
    int n,k; cin>>n>>k;
    vector <int> v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    double l = 0; // l is for sure good
    double r = 1000000005; // r is for sure bad since we cant cut one piece
    rep(i,0,100){ // constant = 100 operations
        int sum = 0; // number of ropes floor()
        double m = (l+r)/2;
        rep(j,0,n){ // iterate through all ropes
            sum += v[j]/m; 
        }
        if(sum >= k){
            l = m;

        }
        else{
            r = m;
        }
    }
    cout<<setprecision(20)<<l<<endl;
   
}
```
 
