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
Product of factors: $\Mu(n) = n^{\tau(n)/2}$ Since every factor can be paired with another factor so that a * b = n.

