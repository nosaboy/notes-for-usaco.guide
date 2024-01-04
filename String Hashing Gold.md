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
We can calculate hsh as such:
```cpp
hsh[0] = 0;
for (int i = 0; i < s.size(); i++) {
	hsh[i + 1] = ((hsh[i] * P) % M + s[i]) % M;
}
```
Moreover, we can precalculate the powers $P^i$ for all $i$:
```cpp
while (pow.size() < s.size()) {
	pow.push_back((pow.back() * P) % M);
}
```
We can then calculate the hash value of any **subtring S[a...b]** using its prefixes:
$$\left(\sum_{i=a}^{b}  P^{b-i} * s[i]\right) \pmod{M} = \left(\sum_{i=0}^{b}  P^{b-i} * s[i] - \left(\sum_{i=0}^{a-1}  P^{a-1-i} * s[i]\right) \cdot P^{b-a+1}\right) \pmod{M}$$
which gives the final equation:
$$(hsh[b+1] - hsh[a]\cdot P^{b-a+1})\pmod{M}$$
```cpp
long long gethash(int start, int end) {
	long long raw_val = (hsh[end + 1] - (hsh[start] * pow[end - start + 1]));
	return (raw_val % M + M) % M;
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
