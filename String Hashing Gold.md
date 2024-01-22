## Usage
- Given two strings, find if any of their substrings are equal in O(1)
- String matching problem in O(n): Given string S and P, find how many times string P occurs as a substring of S
	- Go through each substring in S length |P|, then just compare their hash values.
- Compare lexicographic order of two strings in O(logn) time
	- Use binary search(O(logn)) to find the maximum equal prefix string and compare their hash value(O(1)). Then we just compare the next letter.


### Rolling Hash
Sum of $P^i * s[i]$ over all $i$ positiono of a string. P is a random constant value. This will get big, so we calculate the sum mod $M$.
To check if two strings are equal, we can just check if their **hash values** are equal.

First, compute $hsh[k+1]$ containing the prefix hash of string $s[0...k]$ with length $k+1$. That is, from left to right it is a polynomial: $hsh[k+1] = (s[0]*x^k + s[1]*x^{k-1} + ... + s[k]) mod_{M}$ where we are taking the ascii value of each character. Thus we have
$$hsh[k+1] = \left(\sum_{i=0}^{k}  P^{k-i} * s[i]\right) \pmod{M}.$$
**NOTE:** We must use __int128 mul to multiply stuff or else we will get wrong hash:
```cpp
__int128 mul(ll a, ll b) { return (__int128)a * b; }
ll mod_mul(ll a, ll b) { return mul(a, b) % M; }
```
We can calculate hsh as such:
```cpp
void calchash(string s){
    	hsh[0] = 0;
	for (int i = 0; i < s.size(); i++) {
		hsh[i + 1] = (mul(hsh[i], P) + s[i]) % M;
	}
}
```
Moreover, we can precalculate the powers $P^i$ for all $i$:
```cpp
vector <ll> pw;
while (pw.size() < s.size()) {
	pw.push_back(mod_mul(pw.back(), P) % M);
}
```
We can then calculate the hash value of any **subtring S[a...b]** using its prefixes:
$$\left(\sum_{i=a}^{b}  P^{b-i} * s[i]\right) \pmod{M} = \left(\sum_{i=0}^{b}  P^{b-i} * s[i] - \left(\sum_{i=0}^{a-1}  P^{a-1-i} * s[i]\right) \cdot P^{b-a+1}\right) \pmod{M}$$
which gives the final equation:
$$(hsh[b+1] - hsh[a]\cdot P^{b-a+1})\pmod{M}$$
```cpp
long long gethash(int start, int end) {
	ll raw_val = hsh[end + 1] - mod_mul(hsh[start], pw[end - start + 1]);
	return (raw_val + M) % M;
}
```
**Collision:** When two hash values S(P) and T(P) are equal but string S != T.
Probability of collision by hashing with mod M and string length n is at most $\frac{n}{M}$
Thus, it is better to choose large primes to decrease hash value.
We dont use primes around 10^9 since it has high chance of Collision due to **Birthday Paradox:**

Given hash values of $m$ distinct strings with length up to $n$, the probability of two strings having equal hashes can be up to $\frac{m^2n}{M}.$

**Generating Base:** To avoid collisions, we can generate a random base(P) so the solution is unhackable
```cpp
mt19937 RNG((uint32_t)chrono::steady_clock::now().time_since_epoch().count());
const ll M = (1LL << 61) - 1; // large prime for mod
const ll P = uniform_int_distribution<ll>(0, M - 1)(RNG); // random base
```

**Example 1:** https://dmoj.ca/problem/ccc20s3

We first note that a permutation A of a string B means that A will have the same number of characters as B for each character a - z.
If its a valid permutation, it will have the same characters. Thus, we can just sliding window over all substrings length N and see if its a permutation. However, we want the number of distinct permutations. We can do this by first hashing string H. Then, a permutation is distinct from another if they have different hash values. Thus, if its a permutation we'll insert the hash value into a set. The size of the set in the end will be the number of distinct hash values = the number of distinct string permutations.

**NOTE:** We have |H| strings we want to compare, each with length |N|. Thus, the probability of collision is: $\frac{|H|^2|N|}{M}.$
```cpp
const ll M = (1LL << 61) - 1; // large prime for mod
const ll P = uniform_int_distribution<ll>(0, M - 1)(RNG); // random base
ll hsh[200005]={0}; // LENGTH OF STRING 
vector <ll> pw = {1LL};
__int128 mul(ll a, ll b) { return (__int128)a * b; }
ll mod_mul(ll a, ll b) { return mul(a, b) % M; }
void calchash(string s){
    // precalc pow
    while (pw.size() < s.size()) {
        pw.push_back(mod_mul(pw.back(), P) % M);
    }
    hsh[0] = 0;
		for (int i = 0; i < s.size(); i++) {
			hsh[i + 1] = (mul(hsh[i], P) + s[i]) % M;
		}
}
long long gethash(int start, int end) { // inclusive: string [start...end]
	ll raw_val =
		    hsh[end + 1] - mod_mul(hsh[start], pw[end - start + 1]);
		return (raw_val + M) % M;
}
void solve(){
    string a,s;cin>>a>>s;
    
    int cnt[26]={0}; // count of # of each character in needle
    int pre[s.size()+1][26]={0}; // prefix sum of # of each character in haystack
    // calc prefix sum
    rep(i,0,s.size()){
        rep(j,0,26){
            pre[i+1][j] = pre[i][j];
            pre[i+1][j] += (s[i]-'a' == j) ? 1 : 0;      
        }
    }
    // calc cnt
    rep(i,0,a.size()){
        cnt[a[i]-'a']++;
    }
    calchash(s);
    set <ll> st;
    rep(i,0,s.size() - a.size()+1){
        bool yn = true; // is substr a permutation
        rep(j,0,26){ // check every letter of substr to see if they match
            if(cnt[j] != pre[i+a.size()][j] - pre[i][j]){
                yn = false;
            }
        }
        if(yn){ // substr is valid permutation
            st.insert(gethash(i,i+a.size()-1)); // insert hash value into set
        }
        
    }
    cout<<st.size()<<endl; // number of distinct hash values = number of distinct string
}   
```

#### Two Hashes



**Problem 4:** https://codeforces.com/problemset/problem/1056/E

Hint 1: We should brute force length of "0" and use that to find valid string.
We can have length x if (|t|-c_0 * x) / c_1 where c_0 is number of 0 in string s and c_1 is the number of 1 in s. (|t|-c_0 * x) represents the length of the resulting string after eliminating the "0" from s. If this is divisible, we can note that the length of string "1" is = (|t|-c_0 * x) / c_1 since we split the remaining string into c_1 strings of the same. Thus, we go through s and find the first occurance of "1", then we mark current pos...pos+(|t|-c_0 * x) / c_1 as our string for "1". This takes O(|s|) time since we go through every character in s.
Here, I will give up because I have no clue why this time complexity is acceptable and I wouldnt have came up with this in contest.
On second thought, I think the time complexity is not that bad.
First note we can get the hash of any length string in O(1). Now lets find the upper bound for the length of our string.
We want c_0 * x <= |t|. We do WLOG let c_0 >= c_1( We can just invert 1s and 0s in s if this is not satisfied). Then we want x such that x <= |t|/c_0. We don't know how big c_0 is relatively to whats given, but we know c_0 >= |s|/2. Thus, x <= 2*|t|/|s|. Then for each length x we can calculate whether it satisifies in O(|s|) time. Thus total complexity is 2*|t|/|s| * |s| = 2*t = O(|t|) since we are first looping through length of x, then checking if it satisifes.
- The main takeaway is that in these problem where it seems like brute force is the only option, **try to play around with time complexity formula and minimize as much as possible.** Then **convert this minimum back to something given like n**. Maybe this will return a good time complexity in the end.
- In other words, dont be afraid to play with time complexity equations

```cpp
const ll M = (1LL << 61) - 1; // large prime for mod
const ll P = uniform_int_distribution<ll>(0, M - 1)(RNG); // random base
ll hsh[1000005]={0};
vector <ll> pw = {1LL};
__int128 mul(ll a, ll b) { return (__int128)a * b; }
ll mod_mul(ll a, ll b) { return mul(a, b) % M; }
void calchash(string s){
    // precalc pow
    while (pw.size() < s.size()) {
        pw.push_back(mod_mul(pw.back(), P) % M);
    }
    hsh[0] = 0;
		for (int i = 0; i < s.size(); i++) {
			hsh[i + 1] = (mul(hsh[i], P) + s[i]) % M;
		}
}
long long gethash(int start, int end) {
	ll raw_val =
		    hsh[end + 1] - mod_mul(hsh[start], pw[end - start + 1]);
		return (raw_val + M) % M;
}
void solve(){
    string s,a;cin>>s>>a;
    ll z = 0; ll o = 0;
    rep(i,0,s.size()){
        if(s[i]=='0'){
            z++;
        }
        else{
            o++;
        }
    }
    if(z < o){ // want to make c_0 >= c_1
        // swap
        z=o;
        o=int(s.size())-z;
        rep(i,0,s.size()){
            if(s[i]=='0'){
                s[i]='1';
            }
            else{
                s[i]='0';
            }
        }
    }
    calchash(a);
    ll ans = 0;
    rep(i,1,2*a.size()/int(s.size())){ // 2*|t|/|s|
        ll lenz = i; // length for "0"
        if(z*lenz < a.size() && (a.size()-z * lenz)%o==0){ // (|t|-c_0 * x) / c_1
            ll leno = (a.size()-z * lenz)/o; // length for "0"
            ll valz; // hash val for "0"
            ll valo; // hash val for "1"
		// get the strings valz and valo
            if(s[0]=='0'){ 
                valz = gethash(0,lenz-1LL);
                ll it = 0;
                int j = 0;
                while(j<s.size() && s[j]=='0'){
                    j++;
                    it+=lenz;
                }
                valo = gethash(it,it+leno-1LL);
            }
            else{
                valo = gethash(0,leno-1LL);
                ll it = 0;
                int j = 0;
                while(j<s.size() && s[j]=='1'){
                    j++;
                    it+=leno;
                }
                valz = gethash(it,it+lenz-1LL);
            }
		// go through s to see if it works
            bool ok = true;
            ll it = 0;
            rep(j,0,s.size()){
                if(s[j] == '0'){
                    if(gethash(it,it+lenz-1LL) != valz){
                        ok = false;
                        break;
                    }
                    it+=lenz;
                }
                else{
                    if(gethash(it,it+leno-1LL) != valo){
                        ok = false;
                        break;
                    }
                    it+=leno;
                }
            }
            if(ok && valo != valz && leno > 0LL){ // strings cant be the same, strings cant be empty
                ans++;
            }
        }
        
        
        
    }
    cout<<ans<<"\n";
   
}   
```
