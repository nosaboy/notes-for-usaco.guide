A graph where the outdegree of each node is exactly 1(each node goes to exactly one other node).
### Kth Successor( binary jumping?)
We can thus define f(x,k) as the node we will reach from x and walking k steps. Since each step there is exactly one option, f(x,k) always produces some other node.

With naive approach of simulation, we can query f(x,k) in O(k) time. With smart preprocessing we can query f(x,k) in O(logk) time.

For each node x, lets precalculate all values f(x,k) where k < # of noes and **k is a power of 2**. After this, we know that any number k is made up of powers of 2(in binary representation) so we can just recursively go over each bit. Let the pth bit of k be a 1 and we are starting at x. We can then jump from x to f(x,2^p), so we immediately took 2^p steps. We then set f(x,2^p) as our new starting point. Calculating f(x,k) for any k thus becomes summing up the powers of 2, so f(x,11) = f(f(x,8),3) = f(f(f(x,8),2),1) which takes log(k) time.

To pre calculate each f(x,k), we use the following recursion for all powers of two k:
- if k = 1, f(x,k) = $go[x]$ where go denotes the node x points to.
- if k > 1, f(x,k) = f(f(x,k/2),k/2): Assuming that k/2 is calculated, we go k/2 steps from x and arrive at f(x,k/2), then take another k/2 steps to arrive at f(x,k).

We do this for every node x and for each node x we only have logu numbers that we want to calculate, so the time is O(nlogu) where u is the maximum length of a jump.

### Cycle detection
If we start at a node x and walk, what is the first node in the cycle and how many nodes are in the cycle?

We can keep track of visited cycles. If a node is visited a second time, it is the first node of the sequence and we can easily count length by setting timer: current - initial. This requires O(n) time and memory.

#### Floyd's Algorithm
We only need O(n) time and O(1) memory to detect cycle using Floyd's algorithm(tortise and hare).

**Example 1:** https://codeforces.com/contest/1137/problem/D


**Example 2:** https://codeforces.com/contest/1020/problem/B
This can be solved in O(n^2) by simulating the process:
```cpp
void solve(){
    int n; cin>>n;
    int go[n+1];
    rep(i,0,n){
        cin>>go[i+1];
    }
    rep(i,1,n+1){ // simulate for every node
        int vis[n+1]={0};
        int curr = i;
        while(vis[curr] == 0){
            vis[curr] = 1;
    
            curr = go[curr];

        }
        cout<<curr<<" ";
    }
    cout<<endl;      
}
```

This can also be solved in O(n):

```cpp

```


## Problems
**Problem 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=764

Self Editorial:
We construct a directed functional graph based on given shuffles. Then, we realize that if a position is not contained in some cycle, it will eventually be empty. This is because the leaf has no positions pointing to it, so it will disappear in time 1. Then the node it goes to will become a leaf eventually unless its in a cycle where some node will always point to a node for every node. If its not, eventually every node that points to this node will be empty.
Thus, we can just find all nodes that belongs in a cycle and print that.
```cpp
int p[200005];
int vis[200005];
int it[200005];

void solve(){
    
    int n;cin>>n;
    rep(i,0,n){
        cin>>p[i+1];
    }
    int ans = 0;
    int cnt = 1; // colour
    rep(i,1,n+1){
        if(!vis[i]){
            int osa = i;
            // find a cycle
            int nosa = 1; // time
            while(!vis[osa]){ // visit until cycle found
                vis[osa]=cnt;
                it[osa] = nosa;
                nosa++;
                osa = p[osa];
                
            }     
            if(vis[osa] == cnt){ // was cycle found during current iteration
                ans += nosa - it[osa]; 
            }
            // if cycle was not found in current iteration, we dont have to check it again                          
            cnt++;
        }
    }
    cout<<ans<<endl;

}
```
**Problem 2:** https://cses.fi/problemset/task/1751

Self Editorial:
This is just a classic cycle detection & distance problem. We can start at some node to find a cycle(since it will always exist) after some moves. After finding a cycle starting at node x(osa), we find the distance/size of the cycle using timer it as describe in book. We thus have the size is timer_final(nosa) - timer_initial(it_x). We then fill up every node in the cycle starting at x with size. Then, we start from the original node i and dfs until we reach some node that is a part of a cycle and recursively calculate distance(how far is node n away from some cycle, the answer is cycle_size + distance).
If after going through the node i we directly find some cycle that was visited in another iteration, we simply dfs on that and find dist.

```cpp

int p[200005];
int vis[200005];
int it[200005];
int ans[200005];
void dfs(int n){ // places distance.
    if(!ans[p[n]]){
        dfs(p[n]); 
    }
    ans[n] = ans[p[n]]+1;
        
}

void solve(){
    
    int n;cin>>n;
    rep(i,0,n){
        cin>>p[i+1];
    }
    rep(i,1,n+1){ // for every node
        if(!vis[i]){ // if it wasnt visited before
            int osa = i;
            // find a cycle
            int nosa = 1; // time
            while(!vis[osa]){ // vist stuff until we reach a cycle
                vis[osa]=true;
                it[osa] = nosa;
                nosa++;
                osa = p[osa];
                
            }                                                                                                                                                                                                                                                                                                                                                           
            if(ans[osa] == 0){ // if visited node was not a part of a cycle we've already visited(we havent filled out ans yet)
                // fill cycle with dist
                int diff = nosa - it[osa]; // size of cycle using timer
                while(ans[osa] == 0){ // fil cycle ans with size
                    ans[osa] = diff;
                    osa = p[osa];

                }
                if(!ans[i]){ // if there are still some nodes left over: 1 -> 2 -> 3 -> 2. We put answers on 2 and 3 since it is part of a cycle, but we didnt put answer on 1 yet since we only filled the cycle
                    dfs(i); // we recursively find distance using dfs(dist from cycle + cycle size)
                }
                
            }
            else{ // visited node was already a part of some cycle we visited before, so we dont have to place answer again, we can just dfs directly
                dfs(i);
            }
            
        }
    }
    rep(i,1,n+1){   
        cout<<ans[i]<<" ";
    }
    cout<<endl;

}
```
  
