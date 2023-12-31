# Shortest Path with Unweighted Edges

BFS traverse graph in order of length from origin. Thus, the distance from node 1 to x is the distance at **the first time we visit x**.
We can thus find the shortest distance from 1 to all nodes in O(n+m).

**Example 1:** https://cses.fi/problemset/task/1667/

To keep track of the path, we can just keep track of where each node came from(node with most optimal dist) in array p. After, we start at node n and go back until we reach node 1. For every node we go back to the node it came from.

```cpp
vi aj[100005]; int vis[100005]; int dist[100005];
void solve(){
    int n,m;cin>>n>>m;
    // reset
    rep(i,1,n+1){
        vis[i]=false;aj[i].clear();dist[i]=1000000005;
    }
    rep(i,0,m){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    int p[n+1]; // which node it came from

    // bfs shortest path
    dist[1]=0;
    queue<int> q; q.push(1);
    while(!q.empty()){
        int x = q.front(); q.pop();
        rep(i,0,aj[x].size()){
            if(!vis[aj[x][i]]){
                vis[aj[x][i]]=true;
                dist[aj[x][i]] = dist[x]+1; // dist[x] is most optimal due to BFS
                p[aj[x][i]] = x; // set to parent since x is most optimal
                q.push(aj[x][i]);
            }
        }
    }
    if(dist[n] == 1000000005){ // hasnt been reached
        cout<<"IMPOSSIBLE\n";
        return;
    }   
    vi ans;
    int node = n;
    while(node != 1){
        ans.pb(node);
        node = p[node];
    }
    ans.pb(1);
    reverse(ans.begin(),ans.end());
    
    cout<<dist[n]+1<<endl;
	rep(i,0,ans.size()){
        cout<<ans[i]<<" ";
    }
}   


```
