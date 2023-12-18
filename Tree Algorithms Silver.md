#### Calculate # of nodes in subtree or distance from node to root using dp
```cpp
int dist[MAX_N]; // distance of node to root
int cnt[MAX_N]; // number of nodes in subtree rooted at n
void dfs(int n){
    vis[n] = true;
    cnt[n]++; // subtree contains itself
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dist[aj[n][i]] = dist[n]+1; // distance of child = distance of node + 1
            dfs(aj[n][i]); // dfs child
            cnt[n] += cnt[aj[n][i]]; // we add the # of nodes in subtree rooted at child to # of nodes in subtree of parent
        }
    }
}
```
**Example 1:** https://cses.fi/problemset/task/1674
We just use dp to find the size of node subtree using the size of the subtrees of its children. For calculating cnt_of_x, we do 1 + cnt_of_child for every child. Or we can just count nodes in subtree normally: store cnt_of_x = 1 at the beginning, but we print cnt_of_x - 1 in the final answer since we don't include the node x itself.

```cpp
vi aj[200005];
int cnt[200005]; // number of nodes in subtree rooted at n
int vis[200005];
void dfs(int n){
    vis[n] = true;
    // subtree size doesnt contain itself in this problem
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]); // dfs child
            cnt[n] += cnt[aj[n][i]]+1; // we add the # of nodes in subtree rooted at child to # of nodes in subtree of parent
        }
    }
}
 
 
void solve(){
    int n; cin>>n;
    rep(i,2,n+1){
        int u; cin>>u; 
        aj[i].pb(u);
        aj[u].pb(i);
        cnt[i]=0;
        vis[i]=0;
    }
    dfs(1);
    rep(i,1,n+1){
        cout<<cnt[i]<<" ";
    }    
}

```

### Tree Diameter: https://cses.fi/problemset/task/1131
Calculate maximum length of a path between any 2 nodes in a tree in O(n).
#### DP Approach
First **Root the tree at a random node**. Note that every path of the tree between 2 nodes always contains a "high node", the highest node in the path. We can first fix our highest node x, then the longest path that has x as the highest node is just the sum of the two longest distances from x to a **leaf** in its subtree. 
We need to calculate two function:  
- toleaf(n): the maximum length from node n to a leaf in its subtree
- maxlength(n): the maximum length of path that has n as highest node


toleaf(n) = max(toleaf(child_of_n))+1 
We just pick that max dist from a leaf to children + the node itself.

maxlength(n) = largest toleaf(child_of_n) + second largest toleaf(child_of_n) + 1
We just pick the two largest toleaf(childs) and add their sum, then add the node itself.

```cpp
int toleaf[200005];
int maxlen[200005]; 
vi aj[200005];
int vis[200005];
int ans = 0; // number of vertices in maximum path
void dfs(int n){
	vis[n] = true;
    vector <int> len; len.pb(0); len.pb(0); // if n is a leaf
	rep(i,0,aj[n].size()){
		if(!vis[aj[n][i]]){
			dfs(aj[n][i]);
            len.pb(toleaf[aj[n][i]]); // after dfs we have already calculated toleaf[child]
		}
	}
    sort(len.rbegin(),len.rend());
    toleaf[n] = len[0] + 1;
    maxlen[n] = len[0] + len[1] + 1;
    ans = max(ans,maxlen[n]);
}
 
void solve(){
    int n;cin>>n;
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(1);
    cout<<ans-1<<endl;
 
}
   
```

#### 2 DFS Approach

We choose any node a, dfs and find the furthest node b from that node. Then, we can show that node b is an endpoint of the diameter. We can then dfs b to find the furthest node from that node c. The path from b to c will be the diameter.


### All longest path in O(n)
Calculate for every node the maximum length of a path beginning at that node: If we can find the maximum length starting at every node, the tree diameter problem turns into the max of these lengths.

For every node x, we cam split this into 2 cases: The longest path either goes through a child of x or the parent of x.
For the maximum path length that goes through child of x. Since this path from child x cannot then go back to x, this path is toleaf(x) = max(toleaf(child of x) + 1). We can use dp approach to calculate toleaf(x). 

For the maximum path length that goes through parent of x, we can just calculate max path length of parent and second max path length of parent. This is so if the max length path from parent goes through x itself, we know that this path is impossible since we are constructing x -> parent x -> x, thus we take the second longest path from parent that **does not go back to x**(goes in a different direction). If the first longest path does not pass through x, we take that path.

```cpp

```

### Binary Trees
rooted tree where every node only has 0,1, or 2 children(left and right nodes).  
Ways of traversing a tree:
- pre-order: first process the root, then traverse the left subtree, then traverse the right subtree
- in-order: first traverse the left subtree, then process the root, then traverse the right subtree
- post-order: first traverse the left subtree, then traverse the right subtree, then process the root

We can construct the tree if we know 
- the pre-order and in-order
- the post-order and in-order

**Problem 1:** https://codeforces.com/contest/755/problem/C
If the furthest node reachable by x is y, that means x is in the same tree as y. We can thus create an edge between x and y to denote that they are connected. The number of trees will then be the distinct number of connected components after making the graph.
```cpp
vi aj[200005];
int vis[200005];
void dfs(int n){
    vis[n]=true;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
        }
    }
}
 
void solve(){
    int n; cin>>n;
    rep(i,1,n+1){
        int u;cin>>u;
        aj[i].pb(u);
        aj[u].pb(i);
    }
    int cnt =0;
    rep(i,1,n+1){
        if(!vis[i]){
            cnt++;
            dfs(i);
        }
    }
    cout<<cnt<<endl;    
    
}
```
**Problem 2:** http://www.usaco.org/index.php?page=viewproblem2&cpid=788

Self Editorial:
Since both n and q <= 5000, we can have an O(nq) solution. This allows for brute force. For every query, we can just traverse through the tree starting at starting node. Then directly simulate and update min(curr relevence of path) as we go through.

**What I learned:**
- You can store weights between two edges using adjancency list instead of using a map. Just store a pi $aj[n]$ where pair represents {neighbour, weight}. This way, we can reduce O(log(n)) to O(1).
 
```cpp
vector <pi> aj[5005];
int vis[5005];
int cnt;
int r; // current query relevent
void dfs(int n, int mn){ // {curr node, min curr value on path}
    vis[n]=true;  
    cnt++;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i].first] && min(mn,aj[n][i].second) >= r){ // at least relevence r
            // we visit it     
            dfs(aj[n][i].first,min(mn,aj[n][i].second));        
        }
        
    }
}
void solve(){
    int n,q; cin>>n>>q;
    rep(i,0,n-1){
        int a,b,x;cin>>a>>b>>x;
        aj[a].pb({b,x});
        aj[b].pb({a,x});
    }
    while(q--){
        int a;cin>>r>>a; // relevence, start node
        // reset
        cnt = 0;
        rep(i,1,n+1){
            vis[i] = 0;
        }
        dfs(a,1000000005);
        cout<<cnt-1<<endl; // we dont include start node
    }
    
}
```

**Problem 4:** http://www.usaco.org/index.php?page=viewproblem2&cpid=968

Time: 12 minutes

Self Editorial: 
We know there is EXACTlY one path from any node to another. Thus, if the path is continously the same patch of grass, we know that the farmer will not be happy if their preference was different. We note first that if 2 grass types are both on the path, farmer will alwyas be happy. Moreover, if there is only one grass type and thats the grass type the farmer likes, he will always be happy. 
Thus, we can group nodes into "connected regions" of the same colour, where each node in the region can be visited by all other nodes in the region such that the path only contains that grass type. We do this using dfs. Then, if two nodes are in different regions it means their path contains both grass types. If they are in the same region an their type is different from farmers preference, he will be unhappy since their path will only contain that type.
```cpp
int vis[100005];
vi aj[100005];
int group[100005]; // the region/team of node
int cnt=0; // team number
string s; // type
void dfs(int n, int team){
    vis[n]=true;
    group[n] = team;
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            if(s[n] == s[aj[n][i]]){ // if the other path has same type, they belong to same region
                dfs(aj[n][i],group[n]);
            }
            else{ // create new region/team
                cnt++;
                dfs(aj[n][i],cnt);
            }
            
        }
    }
}

void solve(){
    int n,q;cin>>n>>q;
    cin>>s;
    rep(i,0,n-1){
        int a,b;cin>>a>>b;a--;b--;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(0,cnt);
    while(q--){
        int a,b;cin>>a>>b;a--;b--;
        char c;cin>>c;
        if(group[a] != group[b]){ // different regions
            cout<<1;
        }
        else if(s[a] == c){ // same preference 
            cout<<1;
        }
        else{ // different preference, in same region
            cout<<0;
        }
    }

}
```


**Problem 5:** http://www.usaco.org/index.php?page=viewproblem2&cpid=1062

Time: 15 minutes

Self Editorial:
We first note that if we need more cows, it is always better to double current cow instead of moving a cow to this node. This is because we get at most one cow if we move, but at least one cow if we double. Moreover, it is always optimal to add/double least needed possible. This will minimize the moving of node. For example: A -> B -> C, it is better to do 4 operations: double A, move one to B, double B, move one to C instead of: double A, double A, move one to B, move one to B, move one to C. This is because A move one to B then double B, is the same as double A, move one to B, move one to B. However the first one has less operations(**Formal proof??**).
```cpp
int vis[100005]={0};
vi aj[100005];
int ans=0;

void dfs(int n){
    vis[n]=true;
    int cnt = 1; // currently there is 1 cow from previous barn moving here
    int sz = 0;
    
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            sz++; // number of nodes we must go by moving 1 cow
            ans++; // thus we add total days
            dfs(aj[n][i]); 
        }
    }
    while(cnt <= sz){ // we must also leave 1 to stay in current node, so we double until cnt > sz+1
        cnt*=2; // double current cow in barn
        ans++; // add days
    }
}

void solve(){
    int n;cin>>n;
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(1);
    cout<<ans<<endl;

}
```

**Problem 8:** 

Self Editorial:
Pretty much, we just calculate the two diameters. We note that if we add some edge to combine the two trees, the minimum possible connectiion of the maximum length that USES that additional edge is when we connect exactly half of each diameter together. Thus the length is half of first diameter + half of second diameter + additional edge = (ans1+1)/2 + (ans2+1)/2+1. Otherwise if we don't use that edge, the maximum length either diameter and that remains unchanged no matter what edge we add. Thus, the max length is max(max length that goes through added edge, max of 2 diameters) = max((ans1+1)/2 + (ans2+1)/2+1, max(ans1,ans2)).
**How to prove that the min of the max length that goes through 2 edge is just the sum of half of both diameters + 1?**

```cpp
int toleaf[200005];
int maxlen[200005];
vi aj[200005];
int vis[200005];
int ans = 0;
void dfs(int n){
	vis[n] = true;
    vector <int> len; len.pb(0); len.pb(0); // if n is a leaf
	rep(i,0,aj[n].size()){
		if(!vis[aj[n][i]]){
			dfs(aj[n][i]);
            len.pb(toleaf[aj[n][i]]); // after dfs we have already calculated toleaf[child]
		}
	}
    sort(len.rbegin(),len.rend());
    toleaf[n] = len[0] + 1;
    maxlen[n] = len[0] + len[1] + 1;
    ans = max(ans,maxlen[n]);
}

void solve(){
    int n;cin>>n;
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(1);
    int ans1 = ans-1;
    int m;cin>>m;
    //reset
    ans = 0;
    rep(i,0,m+1){
        maxlen[i]=0;
        toleaf[i]=0;
        vis[i]=0;
        aj[i].clear();
    }

    rep(i,0,m-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(1);

    int ans2 = ans-1;
    cout<<max(ans2,max(ans1,(ans1+1)/2 + (ans2+1)/2+1))<<endl;

}
```
