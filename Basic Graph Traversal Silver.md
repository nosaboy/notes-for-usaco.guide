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
**Floodfill Problem 2:** https://codeforces.com/contest/1365/problem/D
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

**Problem 
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
