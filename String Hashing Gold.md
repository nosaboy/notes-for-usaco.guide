## Usage
- Given two strings, find if any of their substrings are equal in O(1)

### Rolling Hash
Sum of $r^i * s[i]$ over all $i$ positiono of a string. r is a random constant value. This will get big, so we calculate the sum mod $M$.
To check if two strings are equal, we can just check if their **hash values** are equal.

First, compute $hsh[k+1]$ containing the prefix hash of string $s[0...k]$ with length $k+1$. That is, from left to right it is a polynomial: $hsh[k+1] = (s[0]*x^k + s[1]*x^{k-1} + ... + s[k]) mod_{M}$ where we are taking the ascii value of each character. Thus we have
$$hsh[k+1] = \left(\sum_{i=0}^{k}  r^{k-i} * s[i]\right) \pmod{M}.$$
We can calculate hsh as such:
```cpp
hsh[0] = 0;
for (int i = 0; i < s.size(); i++) {
			hsh[i + 1] = ((hsh[i] * r) % M + s[i]) % M;
}
```
We can then calculate the hash value of any **subtring S[a...b]:**
$$\left(\sum_{i=a}^{b}  r^{b-i} * s[i]\right) \pmod{M} = \left(\sum_{i=0}^{b}  r^{b-i} * s[i] - \left(\sum_{i=0}^{a-1}  r^{a-1-i} * s[i]\right) \cdot r^{b-a+1}\right) \pmod{M}$$
which gives the final equation:
$$(hsh[b+1] - hsh[a]\cdot r^{b-a+1})\pmod{M}$$
**Collision:** When two hash values S(r) and T(r) are equal but string S != T.
We can small probability of collision with testcases.

