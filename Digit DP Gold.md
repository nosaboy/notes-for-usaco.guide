### Usage
- Given an integer **range**, find all numbers in that range that satisfy some **property based on a numbers digits**
- Looping through the range and checking each integer is too slow
- We pretty much dp by constructing the "good" numbers, and see if it is in the range
- Use dp states to speed up constructing process

#### GeeksforGeeks Digit DP: https://www.geeksforgeeks.org/digit-dp-introduction/
**Example/Problem 3:** https://www.spoj.com/problems/PR003004/
Problem asks: given a and b, print the sum of all digits from a and b.
We can construct a number using digit dp
- Usually we do digit dp for g(x) where we find all such numbers smaller than x. Thus, the answer for a range is usually g(r) - g(l-1)

Usually the states contains
1. position/index of current digit
2. bool for whether we are under the upper_bound or not
  - For every digit index $i$ we are looping through digit 0 to $upper_bound[i]$
  - If our current number matches all digits of the upper_bound, under = 0
  - Else, we know that something must be smaller before, so we can put every number through 0 to 9
3. Something relating to the problem(what we are trying to find)

In this case, we want to store position, under, and sum of current number. We will iterate through the first digit(largest position) to the last digit. For each digit position, we let the upper_bound digit be $k$, then we want pick every digit from 0 to k as the digit in current position, then calculate the recursion forwards. We then add the sum of these recursions together:
```cpp
int ans = 0;
for (int i=0; i<=k; i++) {
  // picks digit i to be the digit at current pos
   ans += rec(idx+1, newtight, sum+i);
}
dp[idx][tight][sum] = ans; // store dp value
```
To pick a tight/under value, we look at the previous tight value. If its 0 then we know we already went under, so newtight is also 0. If prev = 1 it means that all previous digits we chose is the exact same as the upper_bound digits, we know that newtight = 0 if the digit we pick is smaller than the upper_bound digit, so newtight = 0 for all 0...k-1 and newtight = 1 if we pick k.
We can do this in one line:
```cpp
newTight = previousTight & (i == k)
```
Base case = when we reach the end of the digit array, then we have constructed a valid number, so we return its sum.
We do this for both r and l-1, so the sum in between l...r is just rec(r) - rec(l-1).
```cpp
vi digit;
ll dp[50][2][450]; 
// position
// whether we have gone under(0) or not(1)
// sum <= 50 * 9
ll rec(int pos, int under, int sum){
    if(pos == digit.size()){ // we finished constructing a number
        return dp[pos][under][sum] = sum;
        
    }
    int k = digit[pos]; // upper bound digit
    if(under == 0){ // we can do 0...9
        k = 9;
    }
    ll ans = 0;
    rep(i,0,k+1){
        int newtight = under & (i == k);
        if(dp[pos+1][newtight][sum+i] == -1){
            // havent visited yet
            rec(pos+1, newtight, sum+i);
        }
        ans += dp[pos+1][newtight][sum+i];
    }
    return dp[pos][under][sum] = ans;
}
void solve(){
    ll l,r;cin>>l>>r;
    // reset prev
    digit.clear();
    rep(i,0,50){
        rep(j,0,2){
            rep(l,0,450){
                dp[i][j][l]=-1;
            }
        }
    }
    // get digit from last to first
    l--;
    while(l){
        digit.pb(l%10);
        l/=10;
    }
    reverse(digit.begin(),digit.end());
    ll osa = rec(0,1,0); // initially we are bounded so under = 1
    digit.clear();
    //reset for r
    rep(i,0,50){
        rep(j,0,2){
            rep(l,0,450){
                dp[i][j][l]=-1;
            }
        }
    }
    // get digit from last to first
    while(r){
        digit.pb(r%10);
        r/=10;
    }
    reverse(digit.begin(),digit.end());
    ll nosa = rec(0,1,0);
    cout<<nosa-osa<<endl; // rec(r) - rec(l-1)
}
```

**Example 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=435
We can try to construct an interesting number from all numbers 1 to N, then the answer is just # of interesting integers from 1 to Y - # of interesting integers from 1 to x-1.
We can first fix the digit we want to be half of the digit. We then try to construct a digit and counting the number of times this number appears. At the end, if its bigger than digit.size()/2 we break. Since starting 0s dont count, we must also keep track of whether we already chose a number > 0 previously and if we started, then we must count the number of digits. Thus, the states are:
1. position
2. under
3. if we have put down a number other than 0 yet
4. count of number in construction
```cpp
vi digit;
ll dp[50][2][2][50]; 
// position
// whether we have gone under(0) or not(1)
// number we want
// cnt of number we want
// cnt of sec number we want
void reset(){
    rep(i,0,50){
        rep(j,0,2){
            rep(l,0,2){
                rep(p,0,50){
                   
                        dp[i][j][l][p]=-1;
                    
                   
                }             
            }
        }
    }
}
int num; 
ll rec(int pos, int under, int start, int cnt){
    if(pos == digit.size()){ // we finished constructing a number
        
        if(cnt >= 20 && start == 1){
            return dp[pos][under][start][cnt] = 1;
        }
        return dp[pos][under][start][cnt] = 0;
    }
    int k = digit[pos]; // upper bound digit
    
    
    
    if(under == 0){ // we can do 0...9
        k = 9;
    }
    ll ans = 0;
    rep(i,0,k+1){
        int newtight = under & (i == k);
        int newstart = start;
        if(i == 0){
            newstart = start|0;
            
        }
        else{
            newstart = 1;
        }
        int newcnt = cnt;
        if(newstart){
            if(i == num){
                newcnt++;
            }
            else{
                newcnt--;
            }
        }
        if(dp[pos+1][newtight][newstart][newcnt] == -1){
            rec(pos+1,newtight,newstart,newcnt);
        }
        ans += dp[pos+1][newtight][newstart][newcnt];
        
    }
    return dp[pos][under][start][cnt] = ans;
}
int num1; int num2;
ll dup(int pos, int under, int start, int cnt){
    if(pos == digit.size()){ // we finished constructing a number
        
        if(cnt == 20 && start == 1){
            return dp[pos][under][start][cnt] = 1;
        }
        return dp[pos][under][start][cnt] = 0;
    }
    int k = digit[pos]; // upper bound digit
    
    
    
    if(under == 0){ // we can do 0...9
        k = 9;
    }
    ll ans = 0;
    rep(i,0,k+1){
        int newtight = under & (i == k);
        int newstart = start;
        if(i == 0){
            newstart = start|0;
            
        }
        else{
            newstart = 1;
        }
        int newcnt = cnt;
        if(newstart){
            if(i == num1){
                newcnt++;
            }
            else if(i == num2){
                newcnt--;
            }
            else{
                continue;
            }
        }
        if(dp[pos+1][newtight][newstart][newcnt] == -1){
            dup(pos+1,newtight,newstart,newcnt);
        }
        ans += dp[pos+1][newtight][newstart][newcnt];
        
    }
    return dp[pos][under][start][cnt] = ans;
}
void solve(){
    ll l,r;cin>>l>>r;
    digit.clear();
    
    // get digit from last to first
    l--;
    while(l){
        digit.pb(l%10);
        l/=10;
    }
    reverse(digit.begin(),digit.end());
    ll osa = 0;
    rep(i,0,10){
        reset();
        num = i;
        ll ok = rec(0,1,0,20);
        osa += ok;

    }
    rep(i,0,10){
        rep(j,i+1,10){
       
            reset();
            num1 = i; num2 = j;
            ll ok = dup(0,1,0,20);
            osa -= ok;
        }
        
    }
    digit.clear();
    //reset for r
    // get digit from last to first
    
    while(r){
        digit.pb(r%10);
        r/=10;
    }
    reverse(digit.begin(),digit.end());

    ll nosa = 0;
    rep(i,0,10){
        reset();
        num = i;
        ll ok = rec(0,1,0,20);
        nosa += ok;
        //cout<<i<<" "<<ok<<endl;
    }
    //cout<<nosa<<endl;
    rep(i,0,10){
        rep(j,i+1,10){
            reset();
            num1 = i; num2 = j;
            ll ok = dup(0,1,0,20);
            nosa -= ok;
            //cout<<i<<" "<<j<<" "<<ok<<endl;
        }
        
    }
    
    cout<<nosa-osa<<endl; // rec(r) - rec(l-1)
}
```
