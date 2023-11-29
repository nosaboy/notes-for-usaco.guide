### Basic operations on Bits
- All operations are O(1)
#### AND operator
a & b: for every bit, returns 1 if there is a 1 in both **a and b** and returns 0 if both are not 1s for that bit position.
```cpp
// Check if number is even
if(a & 1 == 0){ // if there isnt a 1 at the last bit position(2^0), it must be even.
  cout<<"even\n";
}
else if(a & 1 == 1){ // there is a 1 at the last bit position
  cout<<"odd\n";
}

// Check if number is divisible by 2^k
// 2^k in binary = 1000...00 one 1 followed by k 0s
// 2^k - 1 in binary = 111...11 k 1s.
// a number is divisible by 2^k if its last k bit positions are all 0s
// since 1000..00 k 0s * x = multiple = multiple...000..00 k 0s.
// Thus, if a is a multiple of 2^k and we do a & (2^k-1), we will not find any 1s in the last k bits.
// So a & (2^k - 1) = 0. Let twok[k] = 2^k, we then have
 if(a & (two[k]-1) == 0){ // last k bits do not contain ANY 1s
  cout<<"even\n";
}
else if(a & (two[k]-1) > 0){ // there is a 1 SOMEWHERE in the last k bits
  cout<<"odd\n";
}
```
#### OR operator
a | b: for every bit, return 1 if there is a 1 in either **a or b** and return 0 if both are 0.

#### XOR operator
a ^ b: for every bit, return 1 if there is **exactly one** 1 in a or b and return 0 if **both are 0s or 1s**
so bit position a = bit position b. 

#### NOT operator
~x: **Invert** all bits, always satisfies the formula $~x = -x-1$ (in binary form of cpp 32/64 ints).

**Example 1:** https://codeforces.com/contest/1556/problem/D
We can find the sum of two numbers using only its AND, OR and XOR operators:
If there is a 1 bit at the kth bit position, it means that both a and b has a 1 bit at the kth position.
If only one of them contains a 1, it would be a XOR b. We can use a OR b and a AND b to get a XOR b since any bit that is a 1 in OR but a 0 in a AND b means only one of a or b has a 1 bit in that position. Thus **a XOR b = (a AND b) XOR (a OR b).**
If both contains a 1 at the kth position, the sum of that would be $2 * 2^k = 2^{k+1}$. If there is only one 1 at the kth position, the sum would just be $2^k$. 
Thus, the sum of the two numbers is a+b = 2 * (a AND b) + (a XOR b) = 2 * (a AND b) + ((a AND b) XOR (a OR b)): a+b = 2*(a&b) + a⊕b = 2*(a&b) + (a&b ⊕ a|b).
Since n >= 3, we can find values of first 3 number a,b,c by finding a+b, a+c, b+c.
Summing up and dividing by 2 we get (2a + 2b + 2c)/2 = a+b+c. 
We can then find each individual value of a,b,c by subtracting:
a = a+b+c - (b+c)
b = a+b+c - (a+c)
c = a+b+c - (b+c)
We then find all other values by finding the sum of a+x, then subtract a value to get a+x - a = x.

#### Bit shifts
x << k: shifts x k to the left, adds k 0s to the right of x = $x \cdot 2^k$
x >> k: delete the last k bits of number = $\lfloor \frac{x}{2^k} \rfloor$

#### Count and Modify Bits
Accessing the kth bit from the right
```cpp
// prints out x in binary
for(int i = 31;i>=0;i--){
  if(x & (1<<k) > 0){ // (1<<k) = 1 followed by k 0s, checks k+1th bit
    // there is a 1 in x at the k+1th bit from the right
    cout<<"1";
  }
  else{
    cout<<"0";
  }
}
```
Setting bits to values
```cpp
x |= (1<<k) // sets the k+1th bit from the right to 1
x &= ~(1<<k) // sets the k+1th bit to 0, ~(1<<k) = make every bit 1 except for k+1th bit = 111...011111...
x ^= (1<<k) // inverts k+1th bit, if k+1th bit of x = 1, 1^1 = 0, if k+1th bit = 0, 0^1 = 1

x &= (x-1) // sets the last bit that is a 1 to 0
x &= -x // sets all bits to 0 except for the last bit that is a 1
x |= (x-1) // inverts all bits to the right of the last 1th bit, so setting them all to 1
```
Check if a number is a power of 2: 2^k = 1000...000 k 0s in binary
```cpp
if(x & (x-1) == 0){
  // 1000...000 one 1 followed by k 0s & 11111...111 k 1s
  return true;
}
else{
  return false;
}
```
Counting bits functions
__builtin_clz(x): count # of 0s before the first 1(32 bit integer)
__builtin_ctz(x): count # of 0s after the last 1
__builtin_popcount(x): count # of 1s in binary of x

You can also have same function with ll after you add ll at the end: __builtin_popcountll(x)
```cpp
int x = 5328; // 00000000000000000001010011010000
cout << __builtin_clz(x) << "\n"; // 19
cout << __builtin_ctz(x) << "\n"; // 4
cout << __builtin_popcount(x) << "\n"; // 5
```
### Counting All $2^n$ Subsets of array length n
We can have a number with n binary digits/bits. 
- 1 represents the value is in the subset
- 0 represents the value is not in the subset
We can check every subset by iterating over 0 to $2^{n-1}$ inclusive.
This contains every possible 1 placements from 000... n 0s to 111... n 1s.
```cpp
vi v[n] = {1,2,3,...};
rep(i,0,two[n]){
  vi subst;
  rep(j,0,n){
    if(i & (1 << j)){
        subst.pb(v[j]); // it is in this subset
    }
  }
  // prints subset
  rep(j,0,subst.size()){
    cout<<subst[j]<<" ";
  }
  cout<<endl;
}
```
### Arithmetic Operations using Bits
#### Addition
We add the bits that don't need a carry, then we carry all digits 1 + 1 over to the next digit and repeat the process.
```cpp
int add(int a, int b) {
	while (b > 0) {
		int carry = a & b; // for all bits such that both a and b have 1, we must carry it over
		a ^= b; // we add all bits where only one of them have 1, so we dont carry
		b = carry << 1; // since we carried 1 + 1 = 2, we must multiply by 2
    // repeat this process until we don't have to carry anymore
	}
	return a;
}
```

#### Multiplication
Let the binary representation of $b$ be $b_nb_{n-1}...b_2b_1b_0$ where $b_i$ is a bit that is either 0 or 1. We can then split $b$ into its binary form to get $a \cdot b = a \cdot (1^{b_0} + 2^{b_1} + 4^{b_2} + ... + 2^{nb_n}) = a1^{b_0} + a2^{b_1} + ... + a2^{nb_n}.$ We can now just find the sum of each bit added together to get product.
```cpp
int prod(int a, int b) {
	int c = 0;
	while (b > 0) {
		if ((b & 1) == 1) { // if at the kth position of b has 1
			c = add(c, a);  // Use the addition function we coded previously
		}
		a <<= 1; // since we are moving onto k+1th position where we multiply a * 2^{k+1}, we must multiply a by 2
		b >>= 1; // move onto the k+1th position by deleting the kth position
	}
	return c;
}
```


### Problems
**Problem 1:** https://codeforces.com/contest/1338/problem/A
I took a brute force approach. If everything is sorted we just print 0. If not we go through every possible time up to 63. If we have k time, it means we can add any number from 0 to 2^k - 1 in order to sort array. The greedy strategy is we always want to add the max time such that it is not bigger than the previous number, so the next number can have more room to be big. Thus we can always add to a number to form max(last number, number + 2^k - 1).

```cpp
void solve(){
    ll n;
    cin>>n;
    vector <ll> v;
    rep(i,0,n){
        ll u; cin>>u; v.pb(u);
        
    }
    bool zero = true;
    rep(i,0,n-1){
        if(v[i] > v[i+1]){
            zero = false;
        }
    }
    if(zero){
        cout<<0<<endl;
        return;
    }
    ll two[63];
    two[0]=1;
    rep(i,1,63){
        two[i] = two[i-1] * 2;
    }
    rep(l,0,63){
        //cout<<two[i]<<endl;
        ll last = v[n-1] + two[l+1]-1;
        bool yn = true;
        //cout<<last<<endl;
        for(int i = n-2;i>=0;i--){
            bool lo = false;
            if(v[i] > last){
                yn = false;
            }
            last = min(v[i] + two[l+1]-1, last);
            
            
        }
       
        
        
        if(yn){
            cout<<l+1<<endl;
            return;
        }
    }
}
```
**Problem 2:** https://codeforces.com/contest/1872/problem/E
I did this problem before

**Problem 3:** https://codeforces.com/contest/1017/problem/D
Damn this was like pretty hard no cap its a 1900 and usaco.guide was like "ya this is a ez problem". I think my main mistake is not realizing that o(2^n * 2^n) is passable. Moreover, I did not think of the fact that you can brute force pairs which I guess I should've. I think I was too focused on how to query and the 5*10^5 scared me a little. I should've realized that there are only 4096 * 4096 pairs. Also I was TLEing cause I used maps instead of arr( I also made same mistake on UPAC 2023) so ya that extra log just TLEd since O(2^{2n}) is already very tight.
**The editorial provides a faster solution so I should review**

```cpp
void solve(){
    int n,m,q; cin>>n>>m>>q;
    vi v;
    rep(i,0,n){
        int u; cin>>u; v.pb(u);
    }
    int mp[4097]={0};
    rep(i,0,m){
        string s; cin>>s; 
        reverse(s.begin(),s.end());
        int two = 1;
        int cnt = 0;
        rep(j,0,n){
            if(s[j] == '1'){
                cnt+=two;
            }
            two*=2;
        }
        mp[cnt]++;
    }
    int value[(1<<n)]={0};
    rep(i,0,(1<<n)){
        int osa =i;
        string s="";
        rep(j,0,n){
            if(osa%2==0){
                s+='0';
            }
            else{
                s+='1';
            }
            osa = osa>>1;
        }
        reverse(s.begin(),s.end());
        // turns int to binary string
        rep(j,0,s.size()){
            if(s[j] == '1'){
                value[i]+= v[j];
            }
        }

    }   

    int dp[(1<<n)][101]={0};
    rep(i,0,(1<<n)){
        rep(j,0,(1<<n)){
            // brute force over all possible pairs
            //cout<<value[(~(i^j)&((1<<n)-1))]<<" "<<(~(i^j)&((1<<n)-1))<<" "<<i<<" "<<j<<endl;
            if(value[(~(i^j)&((1<<n)-1))] <= 100){ // the value of the number the pair forms
                dp[i][value[(~(i^j)&((1<<n)-1))]] += mp[j];
            }
            

        }
        rep(j,1,101){
            dp[i][j] += dp[i][j-1]; // all k smaller will be added onto the larger k
        }
    }
    while(q--){
        int k;
        string s; cin>>s>>k; 
      
        reverse(s.begin(),s.end());
        int two = 1;
        int cnt = 0;
        rep(j,0,n){
            if(s[j] == '1'){
                cnt+=two;
            }
            two*=2;
        }
        
        cout<<dp[cnt][k]<<endl;
    }
}
```
