### Basic operations
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

#### Bit shifts


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
