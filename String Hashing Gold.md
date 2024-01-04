## Usage
- Given two strings, find if any of their substrings are equal in O(1)

### Rolling Hash
Sum of $r^i * s[i]$ over all $i$ positiono of a string. r is a random constant value. This will get big, so we calculate the sum mod m.
To check if two strings are equal, we can just check if their **hash values** are equal.

First, compute $hsh[k+1]$ containing the prefix hash of string $s[0...k]$ with length $k+1$. That is, from left to right it is a polynomial:$hsh[k+1] = (s[0]*x^k + s[1]*x^{k-1} + ... + s[k]) \pmod{m}$ where we are taking the ascii value of each character. Thus we have
$$hsh[k+1] = \left(\sum_{i=0}^{k}  r^{k-i} * s[i]\right) \pmod{m}$$

**Collision:** When two hash values S(r) and T(r) are equal but string S != T.
We can small probability of collision with testcases.

