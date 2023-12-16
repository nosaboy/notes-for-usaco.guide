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
