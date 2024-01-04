## Usage
- Given two strings, find if any of their substrings are equal in O(1)

### Rolling Hash
Sum of $r^i * s[i]$ over all $i$ positiono of a string. r is a random constant value. This will get big, so we calculate the sum mod m.
To check if two strings are equal, we can just check if their **hash values** are equal.

First, compute $pre[k]$ containing the prefix hash of string $s[0...k]$. That is,
$$
\sum_{k=0}^{n}  $r^i * s[i]$
$$
**Collision:** When two hash values S(r) and T(r) are equal but string S != T.
We can small probability of collision with testcases.

