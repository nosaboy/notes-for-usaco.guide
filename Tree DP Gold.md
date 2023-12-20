## Intro to Tree DP

**Example 1:** https://cses.fi/problemset/task/1130

Self Editorial:
We can create two arrays: one_n represents the number of ways such that the current edge n(top of subtree) is not included in the pairing and two_n represents that number of ways such that the current edge connects with its child.
If we root the tree at 1 and perform DP, the final answer is $two[1]$ since $one[n] <= two[n]$ always holds because we note that if we dont include the current node and have the max answer, with all its neighbours pairing with some other node, then we can just pair up current root node with one of its children and get the same answer.
To calculate one_n, we can just sum up all the two_n of its children, since one_n <= two_n will always happen. Then, we calculate two_n by summing up all the two_n of its children. However, we want curr node n to connect/pair with one of its childrens, so we take the max of $one[child] - two[child]$ since we are going from two(curr child is contained in a pair with its children) to one(curr child is not contained in a pair). We then add 1, the edge/pairing we just added connecting this child to node n.
NOTE: mx is originally -1 because we are adding 1 at the end, so if we cant pair node n with any of its children(in the case that node n is a leaf), the answer remains 0
```cpp
int one[200005]={0}; // n does not have a pair, child of n is a pair with one of its child
int two[200005]={0}; // there is a pair at n -> one of the child of n
int vis[200005]={0};
vi aj[200005];
void dfs(int n){
    vis[n] = 1;
    int mx = -1; // adjust for +1 at the end
    rep(i,0,aj[n].size()){
        if(!vis[aj[n][i]]){
            dfs(aj[n][i]);
            one[n] += two[aj[n][i]];
            mx = max(mn, one[aj[n][i]] - two[aj[n][i]]); // maximum of this expression
            two[n] += two[aj[n][i]]; 
        }
    }
    two[n] += mx + 1; // add 1 new edge

}
void solve(){
    int n;cin>>n;
    rep(i,0,n-1){
        int a,b;cin>>a>>b;
        aj[a].pb(b);
        aj[b].pb(a);
    }
    dfs(1);
    cout<<max(one[1],two[1])<<endl;
}
```
**Editorial:**
Solution 1 DP:
Same definition with one_n and two_n. We can calculate each seperately

one_n: We note that only taking pairs of child doesn't change/affect other childrens answers, so we just add all the max(one_child,two_child) up.

two_n: If we pair up n with one of its child, it will affect other childrens answers. If we pick & fix some child u, we cannot have u paired up with one of its children. Thus, the answer is one_u + 1. Then we process the other children: sum of max(one_w,two_w) for all child of n: w such that w!= u.
This can be done in O(1) since we have already calculated sum of max(one_w,two_w) which is just one_n, then subtract max(one_u, two_u). Thus we get two_n = max(two_n, one_u + 1 + one_n - max(one_u,two_u)).

Solution 2 Greedy:
We can just greedily fill edges, since not filling edges will only make the answer worse since filling the closest edge possible will at most damage one other pairing(if filling another edge is more optimal, we damaged this pairing, so we can just pick any edge to fill). Just keep pairing up closest until its not possible.


## Solving For All Roots

**Example 1:** 

### All longest path in O(n): https://cses.fi/problemset/task/1132
Calculate for every node the maximum length of a path beginning at that node: If we can find the maximum length starting at every node, the tree diameter problem turns into the max of these lengths.

For every node x, we cam split this into 2 cases: The longest path either goes through a child of x or the parent of x.
For the maximum path length that goes through child of x. Since this path from child x cannot then go back to x, this path is toleaf(x) = max(toleaf(child of x) + 1). We can use dp approach to calculate toleaf(x). 

For the maximum path length that goes through parent of x, we can just calculate max path length of parent and second max path length of parent. This is so if the max length path from parent goes through x itself, we know that this path is impossible since we are constructing x -> parent of x -> x, thus we take the second longest path from parent that **does not go back to x**(goes in a different direction). If the first longest path does not pass through x, we take that path.

```cpp

```
