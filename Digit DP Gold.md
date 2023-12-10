### Usage
- Given an integer **range**, find all numbers in that range that satisfy some **property based on a numbers digits**
- Looping through the range and checking each integer is too slow
- We pretty much dp by constructing the "good" numbers, and see if it is in the range
**Example 1:** http://www.usaco.org/index.php?page=viewproblem2&cpid=435
We can try to construct an interesting number from all numbers 1 to N, then the answer is just # of interesting integers from 1 to Y - # of interesting integer from 1 to X-1.

