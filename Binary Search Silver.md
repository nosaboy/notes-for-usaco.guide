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
 
### C++ Functions for Binary Search 
- **lower_bound** returns a pointer to the first array element whose value is at least x.
- **upper_bound** returns a pointer to the first array element whose value is larger than x.
- **equal_range** returns both above pointers.
All three built in functions assume that array is sorted and will return array.end() if element doesn't exist.
Calculate how many elements in array a is equal to x using the above three functions:
```cpp
auto a = lower_bound(array, array+n, x);
auto b = upper_bound(array, array+n, x);
cout << b-a << "\n";
auto r = equal_range(array, array+n, x);
cout << r.second-r.first << "\n";
```

We can use binary search to find maximum value of a increasing then decreasing function( like the peak of a mountain).
That is, we want to find maximum k such that f(k-1) < f(k), so k also satisfies f(k) > f(k+1).
To do this if we have f(k) > f(k+1), we go left else if f(k) < f(k+1) we go right.


## General Binary Search for monotonic functions
### Find min such that f(x) is true: 000...00111...11
If f(x) is true, then f(y) is true for all y >= x.
If f(x) is false, then f(y) is false for all y <= x.
```cpp
hi++; // we will return n+1 if we found no element such that f(x) = true
while (lo < hi) { // do not converge
	int mid = lo + (hi - lo) / 2;
	if (f(mid)) {
		hi = mid; // mid works so everything above works
	} else {
		lo = mid + 1; // mid doesnt work so we search the next
		// element in hopes that it works
	}
}
return lo;
```
### Finding Maximum: 111...11000...00
```cpp
// if none of the values in the range work, return lo - 1
lo--;
while (lo < hi) {
	// find the middle of the current range (rounding up)
	int mid = lo + (hi - lo + 1) / 2;
	if (f(mid)) {
		// if mid works, then all numbers smaller than mid also work
		lo = mid;
	} else {
		// if mid does not work, greater values would not work 
  		// so we immediately skip this value and check the one below it
		hi = mid - 1;
	}
}
return lo;
```

**Example 1:** https://codeforces.com/contest/1201/problem/C
Given array n, perform operations: increase a number by 1 k times. What is maximum median after k operations.

We want to make median as large as possible using only k operations. This is monotonic function since if we can make x the median, we can make all numbers smaller than x median as well. We just run binary search on answer and see if its possible. To check good(), we greedily add 1s to closest values until we get n/2 numbers that are bigger than x. If this is possible in k operations, good(x) = true.

```cpp
void solve(){
	
    int n,k; cin>>n>>k;
    vector <int> v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    sort(v.begin(),v.end());
    int lo = -1; int hi = 2000000000;
    while(lo  < hi){
        int mid = lo + (hi - lo + 1) / 2;
     
        int cnt = 0; // number of values >= mid
        ll sum = 0; // # of operations
        // to prevent int overflow, we set to ll
        for(int i = n-1;i>=0;i--){
            if(cnt == n/2+1){
                break;
            }
            if(v[i] < mid){
                sum += mid-v[i];   
            }
            cnt++;
        }
        if(sum <= k){ // all left value works
            lo = mid; 
        }
        else{ // mid doesnt work, so we search the one below
            hi = mid-1;
        }
    }
    cout<<lo<<endl;
}
```

### Common Mistakes when implementing Binary Search

#### Off by 1: If we have left = 0, right = 1, it will create an infinite loop under some conditions
```cpp
while (lo < hi) {
		int mid = (lo + hi) / 2; // if lo = 0, hi = 1, we have (0+1)/2 = 0.
		if (f(mid) <= x) {
			lo = mid; // if this happens, lo = 0 and we repeat with lo = 0, hi = 1.
		} else {
			hi = mid - 1;
		}
}
```
To fix this we change the mid value: 
```cpp
mid = (lo+hi+1)/2
```

#### Negative Bounds
If we do mid = (lo+hi)/2 and they are negative, the /2 will round up instead of rounding down. Thus, we use
```cpp
int mid = lo + (hi - lo) / 2;
```

**Example 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=666
We can use lower_bound and upper_bound function to calculate the range we wanted as shown above with arrays.
```cpp
void solve(){
	
    int n,q; cin>>n>>q;
    int a[n];
    rep(i,0,n){
        cin>>a[i];
    }
    sort(a,a+n);
    while(q--){
        int l,r; cin>>l>>r;
        auto it = lower_bound(a, a+n, l);
        auto itr = upper_bound(a, a+n, r);
        cout << itr-it << "\n";
    }
    
}
```
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=690
We first note that we are trying to find the minimum of something, which might be binary search. We see that for each k we can do some simulation in order to find if its possible( after dance, time does not exceed T), and the function good(k) is monotonic. We can for each k in binary search simulate the total time for k using a multiset and adding the next number to the smallest number in the multiset.
```cpp
void solve(){
    int n,t;cin>>n>>t;
    vi v;
    rep(i,0,n){
        int u;cin>>u; v.pb(u);
    }
    int lo = 0; int hi = n;
    while(lo < hi){
        int mid = (lo+hi)/2;
        multiset <int> ms;
        rep(i,0,mid){
            ms.insert(v[i]);
        }
        rep(i,mid,n){
            auto it = ms.begin();
            int cnt = *it;
            ms.erase(ms.find(cnt));
            ms.insert(cnt + v[i]);
        }
        auto it = ms.end(); --it;
        int mx = *it;
        if(mx <= t){ // takes no more than t minutes
            hi = mid;
        }
        else{
            lo = mid + 1;
        }
    }
    cout<<hi<<endl;
       
}
```

**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=858
At first glance it looks like greedy or bisearch cause we are finding the minimum of some simulation. It is probably easier to bisearch for answer and simulate the process. It is always optimal to take the cows that arrive next in the timeline. Choosing a cow that is not the next to arrive worsens the answer I think, thus we sort cows by arrival time first. Then we simulate the answer and for each bus we take the largest # of cows possible without exceeding mid. Then mid will work if we can take all cows given the m buses(All cows goes on some bus after m buses).
```cpp
void solve(){
    int n,m,c;cin>>n>>m>>c;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end());
    int lo = 0; int hi = 1000000000;
    while(lo < hi){
        int mid = (lo+hi)/2;
        
        int it = 0;
        rep(i,0,m){ // for every bus
            if(it < n){ // we still have cows left
                int cnt = 0; // cows on the bus
                int start = v[it]; // starting cow
                while(it < n && cnt < c && v[it] - start <= mid){
                    // good to append to bus
                    cnt++;
                    it++;
                }
            }
            
        }
        if(it == n){ // mid is good
            hi = mid;
        }
        else{
            lo = mid + 1;
        }
    }
    cout<<hi<<endl;
}
```

**Problem 3:** http://www.usaco.org/index.php?page=viewproblem2&cpid=594
The answer is monotonic(find the minimum) so we think of binary search. Let the fixed radius be R. The greedy strat is just to encompass as much hay as possible. So we start with the first hay at the border of the first radius to the left, so we place the first cow at v0 + R, then the range is v0 to v0 + 2R. We know that all hay in this range wont need another cow to destroy. We then look at the next cow not encompassed by the previous range. Then we see at the end how many cows we need to encompass all hays, and adjust radius using binary search.
```cpp
void solve(){
    int n,k;cin>>n>>k;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end()); // sort on line

    int lo = 0; int hi = 1000000005;
    while(lo < hi){
        int mid = (lo + hi)/2;
        int cnt = 0;
        int x = 0;
        int i = 0;
	// simulating process
        while(i<n){
            int start = v[i];
            cnt++;
            while(i<n && start + 2*mid >= v[i]){
		// if the range of encompasses the hay, we know its included so we dont have to add more cows for this
                i++;
            }
            
        }
        if(cnt <= k){
            // if we used less than or equal to k cows, its valid
            hi = mid;
        }
        else{
            lo = mid+1;
        }
    }
    cout<<hi<<endl;
}
```

**Problem 4:** http://www.usaco.org/index.php?page=viewproblem2&cpid=1038
We do binary search on the answer. For each cow we want to place it in the earliest avaliable segment position as possible, so that it is at least distance mid away from the cow before. we simulate this process in O(m+n) by placing cows in the next "avaliable" position thats in a segment.

```cpp
void solve(){
    int n,m; cin>>n>>m;
    vi v;
    map <int,int> end;
    rep(i,0,m){
        int a,b;cin>>a>>b;
        end[a] = b;
        v.pb(a);
    }
    sort(v.begin(),v.end());
    int lo = 0; int hi = 1000000000;
    while(lo < hi){
        int mid = (lo+hi+1)/2;
        int it = 0;
        int pos = 0;
        int cnt = 0;
        while(it < m && cnt < n){
            pos = max(pos, v[it]);
            if(v[it]<= pos && pos <= end[v[it]]){
                pos += mid;
                cnt++;
            }
            else{
                it++;
            }
        }
        if(cnt >= n){
            lo = mid;
        }
        else{
            hi = mid-1;
        }
    }
    cout<<lo<<endl;

}
```


**Problem 10:** https://cses.fi/problemset/task/1620

**Problem 11:** https://cses.fi/problemset/task/1085
