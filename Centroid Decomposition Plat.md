**Problem of Interest 1:** https://codeforces.com/problemset/problem/1790/F
This can be solved using cheeky sqrt trick or centroid decomp.

First sol: link - https://zhuanlan.zhihu.com/p/601326343
Essentially, we can prove that after the first sqrt(n) operations, the max minimum value between pair of nodes will be <= 
sqrt(n). Worst case scenario(longest dist), the graph is a line. After some optimal placements, we have sqrt(n) nodes with increments of sqrt(n). Thus, the dist must be at most sqrt(n). Brute forcing this will take at most **O(nsqrt(n))** time.

Then, we know that in the brute force solution, if we visit a node it will take O(deg(node)) time to process & check. For every node that is coloured in, we will update ans by looking at its minium distance to another black node that was coloured in previously. This way, we can get min dist of the new node. Then, we will update this distance for every node as brute force proceeds. 

Second smart observation: we will only visit and change a neighbour y from x if its disty > distx + 1. This is because if the node already has a more optimal solution we dont go visit it. Moreover, we will only change if distx + 1 < ans. This is because we want to find a path such that its length < ans. Else if distx + 1 <= ans, we'll just pick the previous ans(which has already been found), and be done. Here, we are betting that sometimes in the future y will be coloured in and ans is decreased. 
We can run BFS solution that ony visits a node satisfying these two constrains. Because of the distx+1<ans constraint, we will only update disty at most sqrt(n) times since ans <= sqrt(n) as proven previously.
Since each visit adds deg(x) to time, we have in total O(sum(deg(x) for all x) * sqrt(n)) = O(nsqrt(n)) as sum(deg(x) for all x) = 2n-2 since there are 2n-2 edges in a tree.

For simplicity, well run BFS brute force for the whole thing since we know time works out.
Since both cases run in O(nsqrt(n)) time, the total algorithm is O(nsqrt(n)) which is passable

**Learned**
- Slick sqrt(n) brute force trick on trees

```cpp
void solve(){
    
    int n;cin>>n;
    int c[n+1];
    cin>>c[0];
    int dist[n+1]; dist[c[0]]=1000000005;
    rep(i,1,n){
        cin>>c[i];
        dist[c[i]]=1000000005;
    }
    vi aj[n+1];
    rep(i,0,n-1){
        
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    int ans = 1000000005;
    rep(i,0,n){
        ans = min(ans,dist[c[i]]); // before colouring it we get max dist
        if(i>0){
            cout<<ans<<" ";
        }
        
        dist[c[i]]= 0; // colour it black
 
        // update dist based off ans
        queue <int> q; q.push(c[i]);
        int vis[n+1]={0};
        while(!q.empty()){
            int x = q.front();q.pop();
            vis[x]=true;
            rep(i,0,aj[x].size()){
                
                if(!vis[aj[x][i]]){
                    if(dist[x]+1<dist[aj[x][i]] && dist[x]+1<ans){
                        dist[aj[x][i]]=dist[x]+1; // update
                        q.push(aj[x][i]);
                    }
                }
            }
        }
        
    }
    cout<<endl;
    
}   
 
```
