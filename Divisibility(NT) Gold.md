### Divisors and Divisibility(Introduction to Number Theory)
- NT restricts the number of solutions in a set of possibilities
A set under an operation is closed if for any two elements of the set under that operation, you always get another element in the set.
- Example: set of integers is closed under operations +,-,*, but not under /

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

#### Prime factoring - O(sqrt(n))
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

#### Sieves of Eratosthenes: Check if prime for all numbers 2 to n - O(nlog(log(n)))



