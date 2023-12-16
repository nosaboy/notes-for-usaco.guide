An algorithm that constructs the final answer by choosing **most optimal choice at the moment**, not taking
any choices back. 

#### Coin Problem with specific coins
Given coins = {1,2,5,10,20,50,100,200} and integer n, determine the minimum amount of coins that sum up exactly to n.

The greedy idea is we always pick that largest coin we can pick possible. This will always work with these specific coins. 
First, we always need at most one of {1,5,10,50,100}. This is because if there is two or more we can always replace two of these coins with 1 of another coin. Suppose the optimal solution contains two 1 coins. We can replace this with one 2 coin so we use less coins.
Moreover, we can only use 2 or 20 twice since using it three times will lead to a less optimal solution: if we use 2 three times 2+2+2 it is more optimal to use 1+5, 20 coins are similar. We also can't use 10+20+20 or 1+2+2 since it gets replaced by 50 and 5. From these observations we can prove: **The most optimal way to create a sum value bigger than x CANNOT only use the values smaller than x.**
Thus, it is always better to always pick the maximum coin possible for each choice.
**NOTE: for general case this doesn't always work. Sometimes we can find counter case and prove by contradiction.**

#### Scheduling Problem: https://cses.fi/problemset/task/1629
Given n events with their starting and ending times, find a schedule that includes as many events as possible.

For every possible position after choosing some events, what is the best position we can be at for us to choose more events? We want to be at the **eariest time possible** so we have more events to choose from. Thus, we always pick a possible event that ends in the earliest time, since the weight of every event to the answer is the same( adds 1 to ans).

```cpp
void solve(){
	
    int n; cin>>n;
    vector <pi> v;
    rep(i,0,n){
        int a,b; cin>>a>>b;
        v.pb({b,a}); 
    }
    sort(v.begin(),v.end()); // sort by ending time
    int end = 0;
    int ans =0;
    rep(i,0,n){
        if(v[i].second >= end){
            end = v[i].first;
            ans++;
        }
    }
    cout<<ans<<endl;
    

```
#### Tasks and Deadlines: https://cses.fi/problemset/task/1630
Given n tasks with durations and deadlines, choose an order to perform the tasks. For each task, we earn d − x points where d is the task’s deadline and x is the moment when we finish the task. What is the largest score we can get?

Consider the duration of two tasks. If a > b, then placing a in front of b is less optimal. This is because if we have consecutive tasks a and b such that a > b and a is in front of b, we can switch. This will add b to the answer since a now starts at pos + b instead of pos, but will also subtract a from answer since b now starts at pos - a instead of pos. Since a > b, + b - a decreases our answer.

```cpp
void solve(){
    int n; cin>>n;
    vector <pi> v;
    rep(i,0,n){
        int a,b;cin>>a>>b;
        v.pb({a,b}); // sort by duration
    }
    sort(v.begin(),v.end());
    ll time = 0;
    ll ans = 0;
    rep(i,0,n){
        time += v[i].first;
        ans += v[i].second - time;
    }
    cout<<ans<<endl;
    
}
```
#### Minimizing Sums
Given n numbers a1,a2,...,an, find a value x that minimizes sum $|a_1-x| + |a_2-x| + ... + |a_n-x|$.

The greedy strategy is to use x = median of array. Making x smaller or bigger will only increase the sum. We can visiualize array a as points on a line and we want to choose a point x on a number line which minimizes the sum of all distances from x to a_i. The best option would obviously be choosing the middle.

Given n numbers a1,a2,...,an, find a value x that minimizes sum $|a_1-x|^2 + |a_2-x|^2 + ... + |a_n-x|^2$.

We can first remove the abs() since squaring a number = squaring its abs(). We thus have $(a_1-x)^2 + (a_2-x)^2 + ... + (a_n-x)^2 = a_1^2 + a_2^2 + ... + a_n^2 + nx^2 - 2x(a_1 + a_2 + ... + a_n)$. Thus we just want to choose x that minimizes $nx^2 - 2x(a_1 + a_2 + ... + a_n)$. We know $(a_1 + a_2 + ... + a_n)$ already so let that be s, so we minimize $nx^2 - 2xs = n(x^2 - (2s/n)x + (s/n)^2) - (s/n)^2 = n(x-s/n)^2 - (s/n)^2.$ This is a parabola with x coordinate of vertex(min y value) at x = s/n, so thats our answer: the average of all numbers.

#### Data Compression: Huffman Coding
Constructs an optimal code for compressing a given string.

**I'll look more into it if this idea actually appears in smth**


### Problems
**Problem 1:** https://cses.fi/problemset/task/1074
We want to find a point on the line such that the distance from all points(stick lengths) to that point is minimal. We can achieve this by always choosing the median(or one of the medians). This is because, well i know cause i did a similar problem but im not quite sure atm.
```cpp
 
void solve(){
    int n;
    cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    ll ans = 0;
    sort(v.begin(),v.end());
    rep(i,0,n){
        ans += abs(v[i] - v[n/2]);
    }
    cout<<ans<<endl;
}

```
**Problem 2:** https://cses.fi/problemset/task/1084
Observations:
- We visualize the people and the apartment ranges on a number line

Self Editorial:
The greedy strat is that for every apartment, we are given some choices for what people to assign it to, and we always pick the **earliest person**. This is because it will not ruin the answer moving forwards and picking someone other than the earliest avaliable person will only ruin the answer.
```cpp
void solve(){
    int n,m,k;cin>>n>>m>>k;
    vi a; vi b;
    rep(i,0,n){
        int u;cin>>u;a.pb(u);
    }
    rep(i,0,n){
        int u;cin>>u;b.pb(u);
    }
    sort(a.begin(),a.end());
    sort(b.begin(),b.end());
    int ans = 0;
    int i = 0; int j =0;
    while(i < n && j < m){
        if(a[i] < b[j] - k){
            i++; // a is too small to be in range, so we move a up
        }
        else if(a[i] > b[j] + k){
            j++; // a is too big to be in range, so we move b up
        }
        else{
            ans++;
            i++; j++;
        }
    }
    cout<<ans<<endl;
    
}

```

**Problem 3:** https://cses.fi/problemset/task/1090
Obersvations:

Self Editorial
Traverse through people in ascending order. For each person we always want to pick the **largest person that will not exceed the limit**. This is because if we pick anyone smaller, the answer will only get worse since a larger small will then get with the largest, which causes more gondolas.
```cpp
void solve(){
    int n,x;cin>>n>>x;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.begin(),v.end());
    int l = 0; int r = n-1;
    int ans = 0;
    while(l <= r){
        if(l==r){
            // there is only 1 person left
            ans++;
            break;
        }
        else{
            if(v[l] + v[r] <= x){
                // successful pairing
                ans++;
                l++; r--;
            }
            else{
                // unsuccessful pairing, we must decrease the sum 
                ans++; // the max guy can only go on a gondola by themselves
                r--;
            }
        }
    }
    cout<<ans<<endl;
    
}

```

**Problem 5:** https://cses.fi/problemset/task/1073
Observations:
- Always process the cube that will cause the least damage

Self written editorial
When processing every cube, we want to put it on top of the smallest possible cube. If its bigger than every cube we create a new tower. This is because if we don't place it on the smallest possible cube, the answer can only get worse since they achieve the same thing, so why not put it on the smallest so the larger ones might store more. 
```cpp
void solve(){
    int n; cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    ll ans = 0;
    multiset<int> ms; // current towers
    rep(i,0,n){
        auto it = ms.upper_bound(v[i]); // check for smallest cube larger than current cube
        if(it == ms.end()){ // curr cube is largest
            ms.insert(v[i]);
        }
        else{
            // we put cube on this tower
            ms.erase(it); 
            ms.insert(v[i]);
        }
    }
    cout<<ms.size()<<endl; // num of towers
    
}
```

**Problem 6:** https://cses.fi/problemset/task/1632
This is just scheduling problem but with multiple people. For each event we see if a person is avaliable to view it. 
```cpp
void solve(){
    int n,k;cin>>n>>k;
    vector <pi> v;
    rep(i,0,n){
        int a,b;cin>>a>>b;
        v.pb({b,a}); 
    }
    sort(v.begin(),v.end()); // sort by earliest end time
    multiset <int> end;
    rep(i,0,k){
        end.insert(0); // k people starting at time 0
    }
    int ans = 0;
    rep(i,0,n){
        auto it = end.upper_bound(v[i].second);
        if(it != end.begin()){ // there is a student whose end time < curr starting time
            ans++; // student will watch this movie
            --it;
            end.erase(it);
            end.insert(v[i].first); // set to curr end time
        }
    }
    cout<<ans<<endl;
}
```

**Problem 9:** http://www.usaco.org/index.php?page=viewproblem2&cpid=835
We note that it is always more optimal to get the cows with the most w. This is because we can more likely prevent cows with smaller w going in the line by filling up with more cows.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
    }
    sort(v.rbegin(),v.rend()); / sort w_i largest to smallest
    int ans = 0;
    rep(i,0,n){
        if(v[i] >= i){ // of wi > cows already added
            ans++; // we can add this cow
        }
        else{ // as line gets bigger, w_i gets smaller so every cow after will not go in the line
            break;
        }
    }
    cout<<ans<<endl;
}
```

**Problem 10:** http://www.usaco.org/index.php?page=viewproblem2&cpid=571
Self Editorial
This problem is tianjicaima but with multiple horses :)
For each Bessie horse, we want to eliminate the largest possible Elsie horse. If for some Bessies horse we do not eliminate the largest Elsie horse, our answer will only get worse. We thus go from largest to smallest value, and try to eliminate the biggest Elsie horse possible. This way smaller Bessie horses have more chance eliminating smaller Elsie horses.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    int vis[2*n+1]={0};
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        vis[u]=1;
    }
    vi a; // Bessies horse
    rep(i,1,2*n+1){
        if(!vis[i]){
            a.pb(i); 
        }
    }
    sort(a.rbegin(),a.rend()); // sort value largest to smallest
    sort(v.rbegin(),v.rend()); // sort value largest to smallest
    int l = 0; int r = 0;
    int ans = 0;
    while(l < n && r < n){
        if(a[l] > v[r]){
            // possible to pair
            ans++;
            l++;r++;
        }
        else{
            // cant pair, got to decrease r
            r++;
        }
    }
    cout<<ans<<endl;
}
```

**Problem 11:** http://www.usaco.org/index.php?page=viewproblem2&cpid=573

Self Editorial:
We realize that a point is a point, so we want to maximize number of points in first half, then number of points in second half using the remaining unused cards. For the first half, we can just sort both decks and use strategy in Problem 10. For second half, it is similar. We sort the Elsies deck by increasing, then for each Elsie card we want to see if there is some value smaller than it. If there is we can pair that up and increase ans by 1.
```cpp
void solve(){
    int n;cin>>n;
    vi v;
    int vis[2*n+1]={0};
    rep(i,0,n){
        int u;cin>>u;v.pb(u);
        vis[u]=1;
    }
    vi a;
    vi b; // first half
    vi c; // second half

	// insert bessies cards
    rep(i,1,2*n+1){
        if(!vis[i]){
            a.pb(i);
        }
    }
	// insert first half
    rep(i,0,n/2){
        b.pb(v[i]);
    }   
    int ans = 0;
    sort(a.rbegin(),a.rend()); // sort value largest to smallest
    sort(b.rbegin(),b.rend()); // sort value largest to smallest
    int l = 0; int r = 0;
    // find the largest possible points for first half
    while(l < n/2 && r < n/2){
        if(a[l] > b[r]){
            // possible to pair
            ans++;
            vis[a[l]]=1; // used a[l]
            l++;r++;
        }
        else{
            // cant pair, got to decrease r
            r++;
        }
    }   
	// insert second half
    rep(i,n/2,n){
        c.pb(v[i]);
    } 
    // push all unused values to second half
    a.clear();
    rep(i,1,2*n+1){
        if(!vis[i]){
            a.pb(i);
        }
    }
    sort(a.begin(),a.end()); // sort smallest to largest
    sort(c.begin(),c.end()); // sort smallest to largest
    l = 0;
    r = 0;
	//  find largest possible points in second half
    while(l < a.size() && r < n/2){
        if(a[l] < c[r]){
            // possible to pair
            ans++;
            l++;r++;
        }
        else{
            // cant pair, make r bigger
            r++;
        }
    }
    cout<<ans<<endl;
}

```

**Problem 13:** http://www.usaco.org/index.php?page=viewproblem2&cpid=810
Observations:
- It is always optimal to stop at the largest tastiness rest stop until we are forced to move
Self Editorial:
The observation holds because if we stop at some rest stop other than the largest, the answer is less optimal because we could've spent that time stopping at the largest stop.
We store the largest stop in front of current position using prefix max. We then simulate while going through each "optimal" rest stop, stopping until farmer john catches up, adding the total tastiness values.

```cpp
void solve(){
    ll l,n,a,b;cin>>l>>n>>a>>b;
    ll diff = a-b;
    vector <pair<ll,ll>> v;
    rep(i,0,n){
        ll x,y;cin>>x>>y;
        v.pb({x,y});
    }
    pair<ll,ll> pre[n]; // maximum valued tastiness range [i...n]
    sort(v.begin(),v.end());
    pre[n-1] = v[n-1]; 
    vector <pair<ll,ll>> ar; // most optimal rest stop
    ar.pb(v[n-1]);
    for(int i = n-2;i>=0;i--){
        if(pre[i+1].second < v[i].second){ // if tastiness is bigger than pref
            pre[i] = v[i];
            ar.pb(v[i]); // most optimal rest stop at this time
        }
        else{
            pre[i] = pre[i+1];
        }
    }
    sort(ar.begin(),ar.end()); // sort by time
    ll time = 0;
    ll ans = 0;
    rep(i,0,ar.size()){
        ans += (ar[i].first-time) * diff * ar[i].second; // formula
        time = ar[i].first;
    }
    cout<<ans<<endl;
}
```

**Problem 14:** http://www.usaco.org/index.php?page=viewproblem2&cpid=714
We realize that we want to greedily order the cow such that after each cow greedily chooses some chicken for all cows, the ans is max. Note that it is always best to just pick a chicken as soon as possible since it wont affect answer if we wait. We note that if we sort cows by end points and process it smallest to largest then pick the earliest chicken possible, it will not ruin some further picking. Consider only two cow ranges, one after another. We will lose if a chicken shares both ranges A and B and another chicken is only present in one cow range A, then we pick the chicken that shares both ranges for cow A. Thus leaving nothing for cow B. Always prioritizing the earliest ending cow prevents this, since the only way for the above scenario happens is if the chicken that only occupies A is in front of the other chicken that occupies both ranges. In this case we always pick the earliest chicken possible, so it will always work out.
```cpp
void solve(){
    int n,m;cin>>n>>m;
    vi a; multiset <int> ms;
    rep(i,0,n){
        int u;cin>>u;a.pb(u);
        ms.insert(u);
    }
    vector <pi> v;
    rep(i,0,m){
        int a,b;
        cin>>a>>b;
        v.pb({b,a});
    }
    sort(v.begin(),v.end()); // sort by end time
    int ans = 0;
    rep(i,0,m){
        auto it = ms.lower_bound(v[i].second); // earliest chicken avaliable 
        if(it != ms.end() && *it <= v[i].first){ // this earliest chicken is inside range
            ms.erase(it); // used this chicken
            ans++;
        }
    }  

    cout<<ans<<endl;  
}
```

**Problem 15:** http://www.usaco.org/index.php?page=viewproblem2&cpid=990

```cpp

```
