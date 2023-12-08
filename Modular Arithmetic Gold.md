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
**NOTE: x has an inverse mod m if and only if x and m are coprime** 
We can calculate the inverse using Eulers theroem(FLT++): $x^{\phi(m)} \equiv 1 \pmod{n}$, so $x^{-1} = x^{\phi(m)-1}$. Where $\phi(m)$ is Eulers Totient Function.

**Calculate using Extended Euclidean algorithm:**
We can solve the linear diophantine equation ax + my = 1 using extended eulicdean algorithm. We can the find the mod inverse of x by taking everything to mod m: $ax + my \equiv ax \equiv 1 \pmod{m}$. The mod inverse of x is thus a.

**Calculate using Eulers Theorem:**
If a and m are coprime, then we can calculate using FLT: $a^{m-1} \equiv 1 \pmod{m},$ so the inverse is $a^{m-2}.$ 
We can calculate mod inverse in O(log(m) + log(m)):
first calculate the Eulers Totient Function of m in O(log(m)) if the prime factorization is known(if it isn't known, we have to calculate prime factors in O(sqrt(m)) time), then use the pow function above to calculate x^{\phi(m)-1}$ in O(log(phi(m))) = O(log(m)).

**Beware: Division in mod takes O(logm) time**
```cpp
```
https://cp-algorithms.com/algebra/module-inverse.html
**Fast inv function(less than 50 operations) using Euclidean Division:**
```cpp
int inv(int a) {
  return a <= 1 ? a : m - (long long)(m/a) * inv(m % a) % m;
}
```

#### Benq ModInt template
https://github.com/bqi343/cp-notebook/blob/master/Implementations/content/number-theory%20(11.1)/Modular%20Arithmetic/ModIntShort.h

### Problems
**Problem 1:** https://cses.fi/problemset/task/1712
We know 10^9+7 is prime, so $a^{10^9+6} equiv 1 \pmod{10^9+7}.$ So the power of a loops back every $10^9+6$. Thus, $a^{b^c} \equiv a^{b^c \pmod{10^9+6}} \pmod{10^9+7}.$ Thus, we first calculate $x = b^c \pmod{10^9+6}$, then calculate $a^x \pmod{10^9+7}$ using power mod function.

```cpp
int pow(int x, int n, int m) {
  if (n == 0) return 1%m;
  long long u = pow(x,n/2,m);
  u = (u*u)%m;
  if (n%2 == 1){
   u = (u*x)%m;
  }
  return u;
}
void solve(){
    int a,b,c;cin>>a>>b>>c;
    cout<<pow(a,pow(b,c,1000000006),1000000007)<<endl;
}
```
