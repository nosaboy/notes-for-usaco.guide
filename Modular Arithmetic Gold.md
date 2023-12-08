https://davidaltizio.web.illinois.edu/ModularArithmetic.pdf
Residue Class - Two numbers a and b are in the same residue mod n if $a \equiv b \pmod{n}$
#### Mod properties
In mod, we can take any number and replace it with another number in the same residue class.
Let a and b be in the same residue class, c and d be in the same residue class, then
$a+c \equiv b+d \pmod{n}$ and $ac \equiv bd$ since they will be the same numbers mod n.

Example 5: What is $17^{198} \pmod{100}$
If we are asked for the last n digits, we can try to find a 11 or 9 mod 10^k, then use **binomial expansion** (10+1)^k to cancel out everything until 10^n. So $(10+1)^k = 10^k + \binom{10^k-1}{1}10^{k-1} + ... + \binom{10^k-1}{1}10^1 + 1$. Then we eliminate everything until we are left with $smth10^{n-1} + ... + 1 \pmod{n}.$


Example 8: How many 4 digit palindromes is divisible by 7
We can sometimes break n into other numbers that sum up to n, then eliminate using mod.

https://usaco.guide/CPH.pdf#page=211
In general, we can always replace a number with its mod m when taking it to mod m in some operation, so the following hold:
- (x+y) mod m = ((x mod m) + (y mod m)) mod m
- (x-y) mod m = ((x mod m) - (y mod m)) mod m
- (x*y) mod m = ((x mod m) * (y mod m)) mod m
Im pretty sure division is different and you would have to use mod inverse.

#### Calculating Exponents
We can preconmpute all exponents from x^0 to x^n in O(n) preprocessing and O(1) query.
We can process any exponent x^n in O(logn) using recursion:
- If n = 0: return 0
- If n is even: return pow(x,n/2) * pow(x,n/2)
- If n is odd: return x * pow(x,(n-1)/2) * pow(x,(n-1)/2)
```cpp
int pow(int x, int n) {
  if (n == 0) return 1%MOD;
  long long u = pow(x,n/2);
  u = (u*u)%MOD;
  if (n%2 == 1){
   u = (u*x)%MOD;
  }
  return u;
}
```
#### Modular Inverse
y is the inverse of x mod m if $xy \equiv 1 \pmod{m}$. The inverse of x mod m is $y = x^{-1}$. We can thus do mod division: We know that $xx^{-1} \equiv 1 \pmod{m}$, so $y/x \equiv x^{-1}\cdot y\pmod{m}.$
Just multiply the numerator by the inverse of the denominator. 
**NOTE: If x has an inverse mod m, then x must be coprime to m.** 
We can calculate the inverse using Eulers theroem(FLT++): $x^{\phi(m)} \equiv 1 \pmod{n}$, so $x^{-1} = x^{\phi(m)-1}$. Where $\phi(m)$ is Eulers Totient Function.

We can calculate mod inverse in O(sqrt(m) + log(phi(m))):
first calculate the Eulers Totient Function of m in O(sqrt(m)), then use the pow function above to calculate x^{\phi(m)-1}$ in O(log(phi(m))).
```cpp
```

