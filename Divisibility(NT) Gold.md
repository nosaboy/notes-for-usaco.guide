### Divisors and Divisibility(Introduction to Number Theory)
- NT restricts the number of solutions in a set of possibilities
A set under an operation is closed if for any two elements of the set under that operation, you always get another element in the set.
- Example: set of integers is closed under operations +,-,*, but not under /

lcm(a,b) = ab/gcd(a,b)

Example 5: How many unordered pairs of positive integers({1,2} = {2,1}) that sum up to 2000 and are coprime?

Note that if two integers a and b are no coprime, they have some common factor c. So we have a = cx, b = cy, so a + b = c(x+y) = 2000. Thus c is a factor of 2000. So if {a,b} satisifies if a and b don't share some factor of 2000. We know that if a does not have any factors in 2000, then b doesn't have any factors of 2000 either. Thus we have values of a can be phi(2000) = 800. It is unordered pairs so answer is 400.

### CPH Chapter 21 Number Theory 

Given prime factorization:
$n = p_1^{a_1}p_2^{a_2}...p_n^{a_b}$

Number of factors: $\tau(n) = \prod_{i=1}^k (a_i+1)$

Sum of factors: $\sigma(n) = \prod_{i=1}^{k} (1+p_i+p_i^2+...+p_i^{a_i}) = \prod_{i=1}^{k} \frac{p_i^{a_i+1}-1}{p-1}$

Product of factors: $\mu(n) = n^{\tau(n)/2}$ Since every factor can be paired with another factor so that a * b = n.

- Goldbach’s conjecture: Each even integer n > 2 can be represented as a sum n = a+b so that both a and b are primes.
- Twin prime conjecture: There is an infinite number of pairs of the form {p, p+2}, where both p and p+2 are primes.
- Legendre’s conjecture: There is always a prime between numbers $n^2$ and $(n+1)^2$, where n is any positive integer.

#### Prime factoring in O(sqrt(n))
We know that every prime factor can be between 2 and sqrtn. Thus we just bruteforce over all 2 to sqrtn and factor as much as possible. If we have some left in the end, that must be the last prime factor > sqrt(n).
```cpp
vector<int> factors(int n) {
  vector<int> primes; // store prime factors of n
  for (int x = 2; x*x <= n; x++) { // brute force over all 2 to sqrt(n)
    while (n%x == 0) { // if its divisible by some prime
      // divide and push as much primes as possible
      primes.push_back(x);
      n /= x;
    }
  }
  if (n > 1) primes.push_back(n);
  return primes;
}
```
To check if this number is prime or not, if the returned primes vector.size() == 1, the prime factor only contains 1 prime number which is itself, so it must be prime. 

#### Sieves of Eratosthenes: Check if prime for all numbers 2 to n in O(nlog(log(n)))
Faster way to precompute all primes from 2 to n. Computes integer array where $v[k] = 0$ if $k$ is prime or $v[k] = x$ when $k$ is composite and $x$ is a factor of k.
For every new prime k we find, we set $v[k] = 0$, then go through every multiple of k from k+1 to n and put $v[ak] = k$ until $ak > n$. So x = {2k, 3k, 4k,...} will all have $v[x] = k.$
```cpp
vi prime; 
void sieve(int n){
	int v[n+1]={0};
	rep(k,2,n+1){
		if(v[k]){ 
		// we have already visited this previously, so its not prime
			continue;
		}
			/*
			We havent visited k, meaning it is not a multiple
			of ANY smaller primt weve visited before. Thus it 
			must be prime. Thus we will visit all multiples of k.
			*/
		for(int x = 2*k;x<=n;x+=k){ 
			v[x]=k;
		}
	}
	rep(i,2,n+1){
		if(v[i] == 0){
			prime.pb(i);
		}
	}
}
```
If we found that k is a prime, we loop through multiples of k in floor(n/k) times. Thus the worst case is if every number is prime, so max time is
$\sum_{k=2}^{n} \lfloor\frac{n}{k}\rfloor = n/2 + n/3 + ... + n/n = nlogn.$ based off of harmonic sum. Since every number is not prime, this runs in O(nlog(log(n))) which is pretty close to O(n) with a **very small** multiplier

#### Euclidean Algorithm: Find gcd of (a,b) in O(log(min(a,b))) 
If b = 0: gcd(a,b) = a
Else: gcd(a,b) = gcd(b, a mod b) switch because now a mod b < b.
We know that if c = gcd(a,b), then c = gcd(a mod b, b) since if c is a factor of b, it must be a factor of a mod b as well.
```cpp
int gcd(int a, int b){
  if(b==0){
    return a;
  }
  else{
    return gcd(b, a % b);
  }
}
```

#### Euler's Totient Function: Find number of positive integers smaller than n and coprime to n in O(sqrt(n))
Number of positive integers smaller than n and coprime to n = $\phi(n) = \prod_{i=1}^{k} p_i^{a_i-1}\cdot(p_i-1)$
After calculating the prime factors and their powers, we can then find phi(n) using above formula.
```cpp

```
