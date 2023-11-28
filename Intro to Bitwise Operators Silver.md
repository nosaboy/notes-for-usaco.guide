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
If only one of them contains a 1, it would be a XOR b. We can use a OR b and a AND b to get a XOR b since any bit that is a 1 in OR but a 0 in a AND b means only one of a or b has a 1 bit in that position. Thus **a XOR b = (a AND b) XOR (a OR b).
If both contains a 1 at the kth position, the sum of that would be $2 * 2^k = 2^{k+1}$. If there is only one 1 at the kth position, the sum would just be $2^k$. 
Thus, the sum of the two numbers is a+b = 2 * (a AND b) + (a XOR b) = 2 * (a AND b) + ((a AND b) XOR (a OR b)): $a+b = 2\cdot(a&b) + a⊕b = 2\cdot(a&b) + (a&b ⊕ a|b)$.
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
### Problems
**Problem 1:** https://codeforces.com/contest/1338/problem/A
I took a brute force approach.
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
