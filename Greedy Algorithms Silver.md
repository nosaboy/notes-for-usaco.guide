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
#### Tasks and Deadlines
Given n tasks with durations and deadlines, choose an order to perform the tasks. For each task, we earn d − x points where d is the task’s deadline and x is the moment when we finish the task. What is the largest score we can get?

Consider the duration of two tasks. If a > b, then placing a in front of b is less optimal. This is because if we have consecutive tasks a and b such that a > b and a is in front of b, we can switch. This will add b to the answer since a now starts at pos + b instead of pos, but will also subtract a from answer since b now starts at pos - a instead of pos. Since a > b, + b - a decreases our answer.

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

