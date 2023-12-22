### Usage
- Connectivity: can two nodes reach each other? # of connected components?
Run a for loop for each node not visited yet, run dfs through that node and mark all of them visited, increase the number of connected components by 1.
**Example 1:** https://cses.fi/problemset/task/1666
Basically, we want the minimum edges needed to make the whole graph connected(connected components = 1). We first count the # of connect components. Then edges needed is just # of connected components - 1 since we just pick one node from each connected component and connected them together.
**Note:** Each edge decreases the # of connected components by 0 or 1.

```cpp
vi aj[100005];
int vis[100005];
void dfs(int n){
    vis[n] = true;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}
void solve(){
    int n,m;cin>>n>>m;
    rep(i,0,m){
        int x,y;cin>>x>>y;
        aj[x].pb(y); aj[y].pb(x);
    }
    int cnt = 0;
    vi ans;
    rep(i,1,n+1){
        if(!vis[i]){
            dfs(i);
            cnt++;
            ans.pb(i);
        }
    }
    cout<<cnt-1<<endl;
    rep(i,0,ans.size()-1){
        cout<<ans[i]<<" "<<ans[i+1]<<endl;
    }
       
}

```

- Finding cycles
   - Directly: Run dfs through some node, it weuriare at a node and we already visited a neighbouring node that is **not its parent**, this means that thre contains a cycle from that node to current node.
   - Check if cycle exists: We count the number of edges & nodes. If the graph is a tree(contains no cycles) it means that there are n nodes and n-1 edges. If there are more than n-1 edges there **must contain a cycle**
- Bipartiteness check: when a graph can be coloured with 2 colours such that every pair of adjacent nodes do not have the same colour
   - Simulate the process using dfs: mark some node blue, then mark all its neighbours red, then search its neighbours and mark their neighbours blue and so on... If we find that an already visited neighbor has the same colour as our current node, it means that the graph is not Bipartite.

**Example 2:** https://cses.fi/problemset/task/1668
We want to find if the graph is bipartite and if so print the colour of each node.
```cpp
vi aj[100005];
int team[100005]={0}; // 0 = undecided, 1 = red, 2 = blue
bool same = false;
void dfs(int n, int colour){
    team[n] = colour + 1; // 1 index
    rep(i,0,aj[n].size()){
        if(!team[aj[n][i]]){
            dfs(aj[n][i], 1-colour); // perform 1-0 = 1, 1-1 = 0 trick
        }
        else{ // check if same team
            if(team[aj[n][i]] == colour+1){
                same = true; // impossible to form 2 teams
            }
        }
    }
}
void solve(){
    int n,m;cin>>n>>m;
    rep(i,0,m){
        int x,y;cin>>x>>y;
        aj[x].pb(y); aj[y].pb(x);
    }
    rep(i,1,n+1){
        if(!team[i]){
            dfs(i,0);
        }
    }
    if(same){
        cout<<"IMPOSSIBLE\n";
        return;
    }
    rep(i,1,n+1){
        cout<<team[i]<<" ";
    }
       
}
 

```
### DFS impl
Searches by keep going until reached the deepest node first, then backtracks
```cpp
void dfs(int n){
   vis[n] = true;
   rep(i,0,aj[n].size()){
      if(!vis[aj[n][i]]){
         dfs(aj[n][i]);
      }
   }
}
```
## Floodfill
Special type of dfs( on a grid)
Goes from (x,y) to (x+1,y), (x-1,y), (x,y+1), (x,y-1)
Will probably MLE for 4000 x 4000 grid.
```cpp
// changes for neighbouring cells
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
void flood(int r,int c){
    // checker for if node can be visited
    if (r < 0 || r >= row_num || c < 0 || c >= col_num || g[r][c] == '#' || vis[r][c]){
        // bad states that we do not want to visit
        return;
    }
    vis[r][c] = true; // mark as visited
    // try all neighbouring cells
    rep(i,0,4){
        flood(r + R_CHANGE[i], c + C_CHANGE[i]);
    }
}
	
```

### Iterative Floodfill
- Uses stack or queues like BFS to store which cells to visit
- Generally uses less memory than recursive floodfill(wont MLE as much)
```cpp
int row_num;
int col_num;
bool vis[2500][2500];
char a[2500][2500];
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
void ff(int r, int c) { // start from (r,c)
	// Note: you can also use a queue and pop from the front for a BFS-based approach
	stack<pi> st;
	st.push({r, c});
	while (!st.empty()) {
		r = st.top().first;
		c = st.top().second;
		st.pop();
		if (r < 0 || r >= row_num || c < 0 || c >= col_num || a[r][c] == '#' || vis[r][c])
			continue;

		vis[r][c] = true;
		for (int i = 0; i < 4; i++) {
			st.push({r + R_CHANGE[i], c + C_CHANGE[i]});
		}
	}
}
```

**Example 1:**

Self Editorial:
This is just find number of connected components using floodfill
```cpp
int row_num;
int col_num;
bool vis[2500][2500]={0};
char a[1005][1005];
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
int ans = 0;
void ff(int r, int c) { // start from (r,c)
	// Note: you can also use a queue and pop from the front for a BFS-based approach
	stack<pi> st;
	st.push({r, c});
	// visit every floor '.' possible to contain in cc
	while (!st.empty()) {
		r = st.top().first;
		c = st.top().second;
		st.pop();
        if (r < 0 || r >= row_num || c < 0 || c >= col_num || a[r][c] == '#' || vis[r][c] == 1){
            continue; 
        }
		vis[r][c] = true;
		for (int i = 0; i < 4; i++) {
            st.push({r + R_CHANGE[i], c + C_CHANGE[i]});     
		}
	}
}
void solve(){
    
    int n,m;cin>>n>>m;
    row_num = n; col_num = m;
    rep(i,0,n){
        string s;cin>>s;
        rep(j,0,m){
            a[i][j] = s[j];
        }
    }
    rep(i,0,n){
        rep(j,0,m){
            if(vis[i][j] == 0 && a[i][j] == '.'){
                ans++; // connected component++
                ff(i,j);
            }
        }
    }
    cout<<ans<<endl;

}
```
### Floodfill Problems
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=895

Self Editorial:
**NOTE: Whenever they say "you can reach one node from any other node", it means they are in the same connectedd component** 
- Moreover, you can visit from any node in this cc, and visit all node through some paths.

We can go through each cc and note the max area/max blocks visited when doing floodfill. For perimeter, a side is a perimeter if we cannot go to it(it has '.' or it doesnt exist on the map). Thus, if we find a perimeter for each block '#', we add it. We then sort the area and perimeter of each cc, and get biggest area smallest perimeter.
```cpp
int row_num;
int col_num;
bool vis[2500][2500]={0};
char a[1005][1005];
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
int cnt = 0; // perimeter
int area = 0;
void ff(int r, int c) { // start from (r,c)
	// Note: you can also use a queue and pop from the front for a BFS-based approach
	stack<pi> st;
	st.push({r, c});
	while (!st.empty()) {
		r = st.top().first;
		c = st.top().second;
        area++; // we visit this block, so we document the area
		st.pop();
		vis[r][c] = true;
		for (int i = 0; i < 4; i++) {
            if (r + R_CHANGE[i] < 0 || r + R_CHANGE[i] >= row_num || c + C_CHANGE[i] < 0 || c + C_CHANGE[i] >= col_num || a[r + R_CHANGE[i]][c + C_CHANGE[i]] == '.'){ // we cannot visit/go through this side, so it must be a perimeter side
                cnt++;  
            }
            else if(vis[r + R_CHANGE[i]][c + C_CHANGE[i]] == 0){ // we can visit this block
                vis[r + R_CHANGE[i]][c + C_CHANGE[i]] = 1;
                st.push({r + R_CHANGE[i], c + C_CHANGE[i]}); // visit it
            }
                
		}
	}
}
void solve(){
    
    int n;cin>>n;
    row_num = n; col_num = n;
    rep(i,0,n){
        string s;cin>>s;
        rep(j,0,n){
            a[i][j] = s[j];
        }
    }
    vector <pi> v; v.pb({0,1000000005}); // if everything was '.'
    rep(i,0,n){
        rep(j,0,n){
            if(vis[i][j] == 0 && a[i][j] == '#'){
                // reset area and perimeter
                cnt = 0; area = 0;
                ff(i,j);
                v.pb({area,1000000005-cnt}); // trick so that when we sort, we can get biggest area smallest perimeter
            }
        }
    }
    sort(v.rbegin(),v.rend());
    cout<<v[0].first<<" "<<-(v[0].second-1000000005)<<endl;

}
```
**Problem 2:** https://codeforces.com/contest/1365/problem/D
We make an obervation that if a BAD is next to a GOOD, it is impossible since the BAD can just take one step and follow the GOOD. Then, we make a greedy assumption that it is most optimal to just "circle" every bad by covering all 4 sides. Everything bigger which blocks BAD will only result in a worse case(**How to prove this?**). Thus, we just circle every BAD, then try to see if we can reach every GOOD from (n,m) using floodfill.
```cpp
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
int vis[55][55];
char g[55][55];
void flood(int r,int c){
    // checker for if node can be visited
    if (r < 0 || r >= 53 || c < 0 || c >= 53 || g[r][c] == '#' || vis[r][c]){
        // bad states that we do not want to visit
        return;
    }
    vis[r][c] = true; // mark as visited
    // try all neighbouring cells
    rep(i,0,4){
        flood(r + R_CHANGE[i], c + C_CHANGE[i]);
    }
}
 
void solve(){
    int n,m;cin>>n>>m;
    
    rep(i,0,55){
        rep(j,0,55){
            vis[i][j]=0;
            g[i][j] = '#';
        }
    } 
    vector <pi> v;
    rep(i,1,n+1){
        rep(j,1,m+1){
            cin>>g[i][j];
            if(g[i][j] == 'G'){
                v.pb({i,j});
            }
        }
    } 
    bool yn = true;
    rep(i,1,n+1){
        rep(j,1,m+1){
            if(g[i][j] == 'B'){
                rep(l,0,4){
                    if(g[i+R_CHANGE[l]][j+C_CHANGE[l]] != 'B'){
                        if(g[i+R_CHANGE[l]][j+C_CHANGE[l]] != 'G'){
			    // we place wall to circle BAD
                            g[i+R_CHANGE[l]][j+C_CHANGE[l]] = '#';
                        }
                        else{ // if BAD is next to GOOD, its impossible
                            yn = false;
                        }
                    }
                    
                }
                
            }
        }
    }   
    flood(n,m); // flood from n,m
    rep(i,0,v.size()){ // check every GOOD to see if it was reached
        if(!vis[v[i].first][v[i].second]){
            yn = false;
        }
    }
    if(yn){
        cout<<"Yes\n";
    }
    else{
        cout<<"No\n";
    }
    
    
}
```

**Problem 3:**

Self Editorial:
This is clearly a binary search problem, since more rating = more chance of visiting nodes. We can just do binary search for answer(D). For each mid, we run flood fill and only visit node if abs difference <= mid. Then we just check if we can visit all waypoints. If we can, we decrease since it is possible solution. Else we must increase.
```cpp
int row_num;
int col_num;
bool vis[2500][2500]={0};
int d;
int a[505][505];
bool way[505][505];
const int R_CHANGE[]{0, 1, 0, -1};
const int C_CHANGE[]{1, 0, -1, 0};
int cnt = 0;
void ff(int r, int c) { // start from (r,c)
	// Note: you can also use a queue and pop from the front for a BFS-based approach
	stack<pi> st;
	st.push({r, c});
	while (!st.empty()) {
		r = st.top().first;
		c = st.top().second;
        
		st.pop();
		if (r < 0 || r >= row_num || c < 0 || c >= col_num || vis[r][c]){
			continue;
        }
        if(way[r][c] == 1){ // reached a waypoint
            cnt++;
        }
		vis[r][c] = true;
		for (int i = 0; i < 4; i++) {
            if(abs(a[r][c] - a[r + R_CHANGE[i]][c + C_CHANGE[i]]) <= d){ // if difference is smaller than mid we can visit it(problem description)
                st.push({r + R_CHANGE[i], c + C_CHANGE[i]});
            }
			
		}
	}
}
void solve(){
    
    int n,m;cin>>n>>m;
    row_num = n; col_num = m;
    rep(i,0,n){
        rep(j,0,m){
            cin>>a[i][j];
        }
    }
    int x = 0;
    int y = 0;
    int sz = 0; // how many waypoints there are
    rep(i,0,n){
        rep(j,0,m){
            cin>>way[i][j];
            if(way[i][j] == 1){
                sz++;
                x = i; y = j; // finding a waypoint
            }
        }
    }
    int lo = 0;
    int hi = 1000000005;
    while(lo < hi){

        int mid = lo + (hi - lo)/2;
        // reset
        cnt = 0;
        d = mid;
        rep(i,0,n){
            rep(j,0,m){
                vis[i][j] = 0;
            }
        }
        ff(x,y); // starting from a waypoint
        if(sz == cnt){ // we have found exactly sz waypoints, meaning we can visit all of them
            hi = mid; // satisfies function, so binary search
        }
        else{
            lo = mid+1;
        }
    }
    cout<<hi<<endl;
}
```


### BFS impl
Searches by "flooding", visiting the earliests node(s) first, used for calculating distance of unweighted graph.
**Queues:** First in, first out - moves like a waiting line. O(1) operations:
- .push(): inserts at the back of the queue
- .pop(): deletes from the front of the queue
- .front(): retrieves the element at the front without removing it.
**Deques:** Supports additional operations on the back. O(1) operations:
- pop_front()
- push_front()
- pop_back()
- push_back()
Acts like an array: $dq[i]$ = ith element
we use queue in BFS to **keep track of next node we should visit**(closest to start)
```cpp
queue<int> q;
q.push(start); // start = starting node

while (!q.empty()) {
   int x = q.front(); // x = current node
	q.pop();
	visited[x] = true;
   rep(i,0,aj[x].size()){
      if(!vis[aj[x][i]]){
         q.push(aj[x][i]);
      }
   }
}
```
### Connected Component Problems
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=644
Self Editorial:
n,m < 3000, so we can do O(n^2). Thus, we can just brute force simulate and check after each deletion of a node how many connected components it has. 
**Can this be faster using DSU?**
```cpp
vi aj[3005];
int vis[3005];
int cnt = 0;
int del[3005];
void dfs(int n){
    if(del[n]){ // if already deleted
	// we skip this node
        vis[n]=1; 
        return;
    }
    vis[n] = true;
    cnt++; // add to number of nodes
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}

void solve(){
    int n,m;cin>>n>>m;
    rep(i,0,m){
        int a,b;
        cin>>a>>b;
        aj[a].pb(b); aj[b].pb(a);
    }

    
    rep(i,0,n){
        cnt = 0;
        rep(j,0,3005){
            vis[j] = false;
        }
        rep(j,1,n+1){
            if(del[j] == 0){ // not deleted yet
                dfs(j); // dfs once then break
                break;
            }
        }
        if(cnt == n-i){ // 1 connected component
            cout<<"YES\n";
        }
        else{
            cout<<"NO\n";
        }
        int u;cin>>u;
        del[u]=1;
        
    }
}
```

**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=668

Self Editorial:
Pretty straight forward tbh. We construct a directed graph, where node i can go to node j if the power of node i >= distance from i to j. Since n <= 200, n^2 is possible. We can then brute force and fix every starting node, then check maximum reachbility using counter. Then we find max cnt for all starting node i. This also takes O(n^2).
```cpp
vi aj[205];
int cnt = 0; // number of nodes reached
int vis[205]={0};
void dfs(int n){
    vis[n]=true;
    cnt++;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}

void solve(){
    int n;cin>>n;
    vector <tuple<double,double,double>> v;
    rep(i,0,n){
        double x,y,p;cin>>x>>y>>p;
        v.pb({x,y,p});
        
    }
    rep(i,0,n){
        rep(j,0,n){
            // distance formula:
            double dist = sqrt((get<0>(v[i]) - get<0>(v[j])) * (get<0>(v[i]) - get<0>(v[j])) + (get<1>(v[i]) - get<1>(v[j])) * (get<1>(v[i]) - get<1>(v[j])));
            if(dist <= get<2>(v[i])){ // v[i] can reach v[j]
            
                aj[i].pb(j); // create directed edge
            }
        }
    }
    int ans = 0;
    rep(i,0,n){
        // reset
        cnt = 0;
        rep(j,0,n){
            vis[j]=0;
        }
        dfs(i); // find reachbility
        ans = max(ans, cnt);

    }
    cout<<ans<<endl;
}
   
```

**Problem 3:** http://www.usaco.org/index.php?page=viewproblem2&cpid=944

Self Editorial:
A trivial fact: Given some points on a xy grid, a fence that incloses all points which minimizes area and perimeter is a fence whose length is max x - min x and height is max y - min y. For every connected component, we find the perimeter of this fence by finding these variables using dfs. Then the final answer is the minimum of all the connected components.
```cpp
vi aj[100005];
int cnt = 0; // number of nodes reached
int vis[100005]={0};
pi coord[100005];
int mnx,mxx,mny,mxy;
void dfs(int n){
    vis[n]=true;
    // calculate min max x and y
    mnx = min(mnx, coord[n].first);
    mxx = max(mxx, coord[n].first);
    mny = min(mny, coord[n].second);
    mxy = max(mxy, coord[n].second);
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}

void solve(){
    int n,m;cin>>n>>m;
    rep(i,1,n+1){
        int x,y;cin>>x>>y;
        coord[i] = {x,y};
    }
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); aj[b].pb(a);
    }
    // go thorugh every connected component
    int ans = 1000000005;
    rep(i,1,n+1){
        if(!vis[i]){
            // reset
            mnx = 1000000005; mxx = 0;
            mny = 1000000005; mxy = 0;
            dfs(i); // go through each connected component
            ans = min(ans, 2 * (mxx - mnx) + 2 * (mxy-mny)); // perimeter
        }
    }
    cout<<ans<<endl;

}
```

**Problem 4:** https://open.kattis.com/problems/birthday

Self Editorial:
Since p <= 100 we can do O(p^3) or some O(p^2c)- very tight but it passed :shrug: 
We can brute force over all pairs. For each pair of people, we can erase its corresponding edge, and see how many nodes we can reach starting from some person. If the total number of nodes reached < n, we know we have not reached everyone and thus we print yes. Basically just brute force + simulation.
```cpp
int fir, sec;
vi aj[105];
int vis[105];
int cnt;
void dfs(int n){
    vis[n]=true;
    cnt++;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            if((fir == n && sec == aj[n][i]) || (sec == n && fir == aj[n][i])){
                // skip this edge since we erased it
            }
            else{
                dfs(aj[n][i]);
            }
            
        }
    }
}
bool gg = 0; // we end program if we reach 0 0.
void solve(){
    if(gg){
        return;
    }
    int n,m;cin>>n>>m;
    if(n== 0 && m == 0){
        // we reached 0 0, so we terminate program
        gg = 1;
        return;
    }
    // reset
    rep(i,0,n+1){
        vis[i]=0;
        aj[i].clear();
    }
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b); aj[b].pb(a);
    }
    // brute force every pair
    int ans = 1000000005;
    rep(i,0,n){
        rep(j,0,n){
            // reset
            fir = i; sec = j;
            cnt = 0;
            rep(l,0,n+1){
                vis[l]=0;
            }
            dfs(i); // count number of visited nodes
            if(cnt < n){ // not all nodes were visited
                cout<<"Yes\n";
                return;
            }   
            
        }
    }
    cout<<"No\n";

}
    
```
**Problem 5:** https://dmoj.ca/problem/acsl1p4

Self Editorial:
Since constrains are really small(n <= 20, k <= 30), we can do O(np) solution. We can brute force by finding for every node, if it is contained in a cycle. First, we note that a cycle exists if we have A win B, B win C, ... , X win A. Thus, we can construct a directed graph, where A -> B if A wins B. Then, A is contained in a cycle if after we dfs starting from A, we visit A again.
**Can we do this in O(n)?**
```cpp
vi aj[105];
int vis[105];
int goal; // starting node we are trying to come back to
bool osa; // did we reach the starting node again?
void dfs(int n){
    vis[n]=true;  
    
    rep(i,0,aj[n].size()){
        if(aj[n][i] == goal){ // one of the neighbours is the starting node
	// we visited A again, so it is in some cycle.
            osa = 1;
        }
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);        
        }
        
    }
}
void solve(){
    int n,m; cin>>n>>m;
    rep(i,0,m){
        int a,b,x,y;cin>>a>>b>>x>>y;
        // create direct edge based on who wins
        if(x > y){
            aj[a].pb(b);
        }
        else{
            aj[b].pb(a);
        }
    }
    int ans =0;
    rep(i,1,n+1){ // for each node
        // reset
        rep(j,1,n+1){
            vis[j] = 0;
        }
        osa = 0; 
        goal = i; // start is current node
        dfs(i);
        if(osa){
            ans++;
        }
    }
    cout<<ans<<endl;
}
```
**Problem 6:** https://cses.fi/problemset/task/1682

Self Editorial:
Pick some random node A. If node A this vertex cannot reach every other node through some path, we have found the answer is NO: A B where A cannot reach B. Else, we know that every other node can be reached by this node A. Now, if we have a node B that cannot reach A, we know that the answer is NO: B A. Now assume that every node can reach this A and A can reach every node. Therefore, the graph must be connected.

What I learned:
Here, we found that if there is a graph such that every node can visit every other node, it must mean that for ANY node in the graph, it can reach all other nodes, and it can be reached by all other nodes.
^**This is the literal definition**
To check this, we dfs with normal direction with A -> B(this visits all nodes B that can be reached by A), then reverse the directions so we go from A to B if B -> A(this visits all nodes B that can reach A). Moreover, if ONE of these nodes satisfied this condition^, they will all satisfy
```cpp
int vis[100005];
vi aj[100005];
vi opaj[100005];

void dfs(int n){
    vis[n]=true;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}
void opdfs(int n){
    vis[n]=true;
    rep(i,0,opaj[n].size()){
        if(!vis[opaj[n][i]]){
            opdfs(opaj[n][i]);
        }
    }
}

void solve(){
    int n,m;cin>>n>>m;
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        opaj[b].pb(a);
    }
    rep(i,1,n+1){
        vis[i]=0;
    }
	// dfs in same direction as edge
    dfs(1);
    rep(i,1,n+1){
        if(vis[i] == 0){
            cout<<"NO\n";
            cout<<1<<" "<<i<<endl;
            return;
        }
    }
    rep(i,1,n+1){
        vis[i]=0;
    }
	// dfs in diff direction as edge
    opdfs(1);
    rep(i,1,n+1){
        if(vis[i] == 0){
            cout<<"NO\n";
            cout<<i<<" "<<1<<endl;
            return;
        }
    }
    cout<<"YES\n";
}
```

**Problem 7:** http://www.usaco.org/index.php?page=viewproblem2&cpid=669

Time: 13ish minutes

Self Editorial:
We can just binary search for answer since O(n^2logn) passes. We can binary search for answer. For eac X, construct a graph where two nodes A and B are connected if their distance^2 <= X. Then, run dfs and see if the graph is connected(if we visited all n vertices after dfs). Since the answer X is monotonic(if X works, Y works where Y > X), we can binary search.
```cpp
int vis[100005];
vi aj[100005];
int cnt;

void dfs(int n){
    vis[n]=true;
    cnt++;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}

void solve(){
    int n;cin>>n;
    pi v[n];
    rep(i,0,n){
        int a,b;
        cin>>a>>b;
        v[i]={a,b};
    }

    int lo = 0; int hi = 1000000005;
    while(lo < hi){
        int mid = lo + (hi - lo) / 2;
        rep(i,0,n){
            aj[i].clear();
            vis[i]=0;
        }
        cnt = 0;
        rep(i,0,n){
            rep(j,i+1,n){
                int dist = (v[i].first - v[j].first) * (v[i].first - v[j].first) + (v[i].second - v[j].second) * (v[i].second - v[j].second);
                if(dist <= mid){
                    aj[i].pb(j);
                    aj[j].pb(i);
                }
            }
        }
        dfs(0);
		if (cnt == n) { // we can visit all nodes
			hi = mid;
		} else {
			lo = mid + 1;
		}
    }
    cout<<hi<<endl;
}
    
```

**Problem 8:** http://www.usaco.org/index.php?page=viewproblem2&cpid=992

Time: 20 minutes. Thought this was pretty ez solve.

Self Editorial:

We note that given some swaps, every number at position i can only go to position a_i if there is a series of wormholes that connects i to a_i. Thus, for every cow at i to go to its desired position at a_i, i must be in the same connected component as a_i. Thus, we can create a graph where a wormhole is the edge that connects two positions(nodes). And therefore, the problem turns into finding that max width such that all wormholes with w_i >= width. This hints for binary search since less width than the maximum will still create the same edges(and even more). Thus, we binary search the maximum width. Then, we add an edge/wormhole if the width >= mid. Then, we do dfs and see for every i if i and a_i are in the same connected component. If every i is satisfied, we know that the cows can sort themselves, otherwise they cannot.
```cpp
int team[200005]={0};
int vis[200005]={0};
vi aj[200005];
void dfs(int n, int group){
    vis[n] = 1;
    team[n] = group;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i],group);
        }
    }


}
void solve(){
    int n,m;cin>>n>>m;
    int a[n];
    rep(i,0,n){
        cin>>a[i];
    }
    vector <tuple<int,int,int>> v;
    rep(i,0,m){
        int x,y,w;cin>>x>>y>>w;
        v.pb({w,x,y});
    }
   
    int lo = 0; int hi = 1000000005;
    lo--;
    while (lo < hi) {
	int mid = lo + (hi - lo + 1) / 2; 
        // reset edges
        rep(i,0,n){
            aj[i+1].clear();
            vis[i+1]=0;
            team[i+1]=0;
        }

	// go through each wormhole and connect edges based on mid
        rep(i,0,m){
            if(get<0>(v[i]) >= mid){ // we can connect this edge
                aj[get<1>(v[i])].pb(get<2>(v[i])); 
                aj[get<2>(v[i])].pb(get<1>(v[i]));
            }
        }
        int cnt = 1; // team for connected components
        rep(i,0,n){
            if(!vis[i+1]){
                dfs(i+1, cnt); // node, team
                cnt++;
            }
        }
        bool yn = 1; // can all node go to its desired position
        rep(i,0,n){
            if(team[a[i]] == team[i+1]){ // a_i can go to position i+1 through some connected edges

            }
            else{
                yn = 0;
            }
        }
		if (yn) {
			lo = mid;
		} else {
			hi = mid - 1;
		}
	}
    if(lo == 1000000005){ // its impossible
        cout<<-1<<endl;
    }
    else{
        cout<<lo<<endl;
    }
}
```

**Problem 9:** http://www.usaco.org/index.php?page=viewproblem2&cpid=1159

Time: a long time

Mistakes:
- Wasted a while cause I had the wrong idea about the greedily solution
   - I thought you can just choose a node A in connected component of 1 and a node C in connected component of n, then choose any node B. The answer is min(A -> B -> C)
^^^ like 1 hr i think?
- use long long

Self Editorial:
We first note that 1 can travel to all nodes in the cc(connected component) of 1 and n can travel to all nodes in the cc of n. More generally, if we connect a cc A with another cc V, then all nodes in A can go to all nodes in B. We can only build at most 2 other edges. Thus, we cannot connect to more than 2 cc other than A(cc containing 1) and D(cc containing 2): A -> B -> C -> D which needs at least 3 edges. Thus, we can only choose one cc to connect A and D to.
Thus, we iterate through every cc and calculate the minimum difference i - j between any node in the chosen cc and the nodes in cc of 1 and cc of n. We achieve this by storing the nodes in cc of 1 and cc of n in multisets then using binary search(lower_bound) to check the biggest number of the left of curr node in current cc and the smallest number of the right( the two closest numbers). Also we note that choosing to build 2 roads(even if tehy connect to themselves, costing 0) will always be better. Thus we just go through every cc and find the minimum cost to connect cc of 1 to curr cc and cc of n to curr cc. 

```cpp
int team[200005]={0}; // cc
int vis[200005]={0};
vi aj[200005];
// assign nodes to cc
void dfs(int n, int group){
    vis[n] = 1;
    team[n] = group; // assigns to ccc
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i],group);
        }
    }


}
void solve(){
    int n,m;cin>>n>>m;
    // reset
    rep(i,0,n+1){
        vis[i]=0;
        aj[i].clear();
        team[i]=0;
    }

    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    int cnt = 1; // number of cc
    
    rep(i,1,n+1){
        if(!vis[i]){
            dfs(i,cnt);
            cnt++;
        }
    }
    vector<ll> cc[cnt]; // store nodes contains in each cc
    multiset <ll> a; // connected comonent for 1
    multiset <ll> b; // connected component for n
    rep(i,1,n+1){
        if(team[i] == team[1]){
            a.insert(i);
        }
        if(team[i] == team[n]){
            b.insert(i);
        }
        cc[team[i]].pb(i); // insert node into its team
    }
    ll ans = 100000000000000005;
    rep(i,1,cnt){ // for every connected component
        ll osa = 100000000000000005; // minimum i - j between curr cc and cc of 1
        ll nosa = 100000000000000005; // minimum i - j between curr cc and cc of n
        rep(j,0,cc[i].size()){ // for each node in connected component
            // check closest left and right elements in cc of 1
            auto it = a.lower_bound(cc[i][j]);
            if(it != a.end()){
                osa = min(osa, *it - cc[i][j]); // difference of something >= v_i

            }
            if(it != a.begin()){
                --it;
                osa = min(osa, cc[i][j]-*it); // difference of soemthing < v_i
            }
            // check closest left and right elements in cc of n
            auto itr = b.lower_bound(cc[i][j]);
            if(itr != b.end()){
                nosa = min(nosa, *itr - cc[i][j]); // difference of something >= v_i

            }
            if(itr != b.begin()){
                --itr;
                nosa = min(nosa, cc[i][j]-*itr); // difference of soemthing < v_i
            }
        }
        ans = min(ans, osa*osa + nosa*nosa); // smallest dist with curr cc i
    }
    cout<<ans<<endl;
}

```

**Problem 10:**
Time: 20 - 30 minutes

- Solutions / constructing the graph kinda hard to come up with
- We have to think about what makes taking a number invalid for some cow
	- answer: we cannot take a number a_i if its path does not produce a cycle back to i since someone must take i.
 - TLEd because I didnt put "break" even though both are O(n^3)? Kinda sus 500^3 time limit idk.


Self Editorial:
We first see for each cow what numbers will be illegal(not answer). We note that all numbers to the right of i in the array will be useless, since we can just choose i which will not affect any other choosings and be done. Since this is ranked higher than all numbers to the right of it, it is better to pick i. Now we look at all the numbers to the left of i(ranked higher). We note that if we pick some number a_i, then the a_ith cow cannot pick a_i anymore, so it will have to pick some other number ranked higher than it in teh a_ith row. Moreover, i must now be picked by some other cow. Therefore, we note that if we pick the a_i, a number in front of i for the ith cow, then we must go to a_ith cow and always pick some cow in front of its a_i. We must do this until we arrive at a_i for which i is in front of a_i at the a_ith row/cow, at which we complete the cycle and everyone is happy and nothing is left out. We can find for some a_i in the ith row if it goes back to i in a cycle by first creating a directed edge fir the ith cow: i -> a_i for all a_i in front of the number i. Then, for every a_i we dfs(a_i) and see if we can arrive back to node i. If we can, we pick this present as the max since there will be a colmbination that works since we completed the cycle.

```cpp
int vis[200005]={0};
vi aj[200005];
// visits all nodes to see if it goes back and visits original node(cycles)
void dfs(int n){
    vis[n] = 1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }


}
void solve(){
    int n;cin>>n;
    vi v[n];
    rep(i,0,n){
        rep(j,0,n){
            int u;cin>>u;v[i].pb(u);
        }
    }
    rep(i,0,n){
        rep(j,0,n){
            if(v[i][j] == i+1){ 
                break;
            }
            aj[i+1].pb(v[i][j]); // make directed edge with all numbers a_i in front of i
        }
    }

    rep(i,0,n){
        int ans = i+1; // note that we can just pick i and create a cycle of 1 and be done
        rep(j,0,n){
            if(v[i][j] == i+1){
                break;
            }
            // reset visited
            rep(i,1,n+1){
                vis[i]=0;
            }
            dfs(v[i][j]); // we picked this number, so some path starting from this number must cycle and go back to i
            if(vis[i+1] == 1){ // if i was visited, it mean this was cycled
                ans = v[i][j]; // this is the earliest number that cycles, so we found ans
                break;
            }

        }
        cout<<ans<<endl;
    }
    
}
```
### Two Colouring Problems
**Problem 1:** https://codeforces.com/contest/862/problem/B
We can first find the number of red and blue nodes by simulating colouring as above. Then, we know that it is safe to add an edge to two nodes if they have different colours. In order to maximize edges, we must connect each red node to as many blue nodes possible if they are not connected already. This means the max is just the sum of the number of blue nodes not connected to a red node for every red node. So sum of # of blue - neighbour of x for every red node x.

```cpp
vi aj[100005];
int team[100005]={0}; // 0 = undecided, 1 = red, 2 = blue
bool same = false;
void dfs(int n, int colour){
    team[n] = colour + 1; // 1 index
    rep(i,0,aj[n].size()){
        if(!team[aj[n][i]]){
            dfs(aj[n][i], 1-colour); // perform 1-0 = 1, 1-1 = 0 trick
        }
        else{ // check if same team
            if(team[aj[n][i]] == colour+1){
                same = true; // impossible to form 2 teams
            }
        }
    }
}
void solve(){
    int n;cin>>n;
    rep(i,0,n-1){
        int x,y;cin>>x>>y;
        aj[x].pb(y); aj[y].pb(x);
    }
    rep(i,1,n+1){
        if(!team[i]){
            dfs(i,0);
        }
    }

    int blue = 0;
    rep(i,1,n+1){
        if(team[i] == 2){
            blue++;
        }
     
    }
    ll ans = 0;
    rep(i,1,n+1){
        if(team[i] == 1){ // node is coloured red
            ans += blue - aj[i].size(); // all neighbours must be blue
            // an are already connected to node i, so we can form new
            // edges for every blue node not connected to i
        }
       
    }
    cout<<ans<<endl;
       
}

```
**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=920
We note that for each connected component of colouring, if it works then there are 2 ways to arrange(switch up the colours) since we must satisfiy some constraint between same and different colourings. Thus if there are x connect components and all of their constraints can be satisifed, the answer will be 2^x. If ONE of the connect components have constrains that cannot be satsfied(if S x y and D x y both occur, they cannot be satsfied), then the total answer is 0.
We must store all the SAME & DIFFERENT edges, then use 2 colouring to check if its possible.
```cpp
int vis[100005]={0};
int colour[100005]={0};
vector <pi> aj[100005];
bool possible = true; // is colouring possible
void dfs(int n){
    vis[n]=1;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i].first]){
            if(aj[n][i].second == 1){ // same
                colour[aj[n][i].first] = colour[n];
            }
            else{ // diff
                colour[aj[n][i].first] = 3- colour[n];
            }
            
            dfs(aj[n][i].first);
        }
        else{
            // check if colours satisfy condition
            if(aj[n][i].second == 1){ // same
                if(colour[aj[n][i].first] != colour[n]){
                    possible = false;
                }
            }
            else{ // diff
                if(colour[aj[n][i].first] != 3-colour[n]){
                    possible = false;
                }
            }
        }
        
    }
}
void solve(){
    int n,k;cin>>n>>k;
    rep(i,0,n+1){
        vis[i]=0;
	    colour[i]=0;
    }
    rep(i,0,k){
        char c; int x,y;cin>>c>>x>>y;
        // add edge to count connected components
        aj[x].pb({y, (c == 'S') ? 1 : 0}); aj[y].pb({x,(c == 'S') ? 1 : 0});
	// 1 = same, 0 = diff
        
    }

    // count connected components
    int cnt = 0;
    rep(i,1,n+1){
        if(!vis[i]){
	        cnt++;	
            dfs(i);
        }
    }
    // is all conditions possible to satisfy
    if(!possible){
        cout<<0;
        return;
    }
    // print 2^cnt in binary
    cout<<1;
    rep(i,0,cnt){
        cout<<0;
    }
    
}
```
**Problem 3:** https://codeforces.com/contest/1176/problem/E
If we have have two colours, then we can choose all nodes coloured red so that every other coloured blue must be adjacent to the nodes coloured red(the nodes we took out). We know that since we colour each node in two colours, there must be one colour that does not exceed floor(n/2). Thus, we pick that the nodes that have the smaller colour.

```cpp
vi aj[200005];
int team[200005]={0}; // 0 = undecided, 1 = red, 2 = blue
bool same = false;
void dfs(int n, int colour){
    team[n] = colour + 1; // 1 index
    rep(i,0,aj[n].size()){
        if(!team[aj[n][i]]){
            dfs(aj[n][i], 1-colour); // perform 1-0 = 1, 1-1 = 0 trick
        }
        else{ // check if same team
            if(team[aj[n][i]] == colour+1){
                same = true; // impossible to form 2 teams
            }
        }
    }
}
void solve(){
    int n,m;cin>>n>>m;
    rep(i,1,n+1){
        team[i]= 0; // reset
        aj[i].clear();
    }
    rep(i,0,m){
        int x,y;cin>>x>>y;
        aj[x].pb(y); aj[y].pb(x);
    }
    rep(i,1,n+1){
        if(!team[i]){
            dfs(i,0);
        }
    }

    int blue = 0;
    rep(i,1,n+1){
        if(team[i] == 2){
            blue++;
        }
     
    }
    if(blue > n-blue){ // red <= floor(n/2)
        cout<<n-blue<<endl;
        rep(i,1,n+1){
            if(team[i] == 1){
                cout<<i<<" ";
            }
        }
        cout<<endl;
    }
    else{
        cout<<blue<<endl;
        rep(i,1,n+1){
            if(team[i] == 2){
                cout<<i<<" ";
            }
        }
        cout<<endl;
    }

       
}
```
