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

**Given array a and query that have two values l,r, determine number of integers inside range l...r inclusive.
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
