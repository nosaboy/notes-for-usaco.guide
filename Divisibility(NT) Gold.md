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

**Example 1:** https://cses.fi/problemset/task/1713
For each query, we can just compute each prime factor and its exponents in O(sqrt(x)), then use the # of divisors formula to find answer. Time is O(nsqrt(x)).
```cpp
void solve(){
    int n;cin>>n;
    rep(i,0,n){
        int u;cin>>u;
        int ans = 1;
        rep(j,2,sqrt(u)+1){
            if(u%j==0){
                int cnt = 0;
                while(u%j==0){
                    u/=j;
                    cnt++;
                }
                ans *= cnt+1;
 
            }
        }
        if(u>1){
            ans*=2;
        }
        cout<<ans<<endl;
 
    }
    
}
```
We can speed this up using Sieves of Eratosthenes. 
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

**Sieves in LINEAR TIME:** https://codeforces.com/blog/entry/54090

CSES Counting Divisors using Sieves in O(xlog(log(x))): Cuts down to 1/3 of O(nsqrt(x)) solution time.
We first calculate all possible primes. Then, we just cycle through all primes instead of recomputing primes in sqrt(x) for every x.
- **NOTE: $x$ can have $\mathcal{O}(\log x)$ distinct prime factors**
```cpp
vi prime; 
void sieve(int n){
	int v[n+1]={0};
	rep(k,2,n+1){
		if(v[k]){ 
			continue;
		}
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
void solve(){
    int n;cin>>n;
    sieve(1000005);
    rep(i,0,n){
        int u;cin>>u;
        int ans = 1;
        rep(j,0,prime.size()){
            if(prime[j] > sqrt(u)){ // save more time
                break;
            }
            if(u%prime[j]==0){
                int cnt = 0;
                while(u%prime[j]==0){
                    u/=prime[j];
                    cnt++;
                }
                ans *= cnt+1;

            }
        }
        if(u>1){
            ans*=2;
        }
        cout<<ans<<endl;

    }
    
}
```
GCD and LCM is transitive: gcd(a,b,c,d) = gcd(a,gcd(b,gcd(c,d)))
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
We can find lcm = a/gcd(a,b) * b
```cpp
int lcm(a,b){
  return a/gcd(a,b) * b;
}
```
**IMPORTANT:** gcd and lcm are **both associative:**
$\gcd(a_1, a_2, a_3, a_4) = \gcd(a_1, \gcd(a_2, \gcd(a_3, a_4))).$

#### Euler's Totient Function: Find number of positive integers smaller than n and coprime to n in O(sqrt(n))
Number of positive integers smaller than n and coprime to n = $\phi(n) = \prod_{i=1}^{k} p_i^{a_i-1}\cdot(p_i-1)$
```cpp

```

### MONT Chapter 1 Divisibility
...didnt complete


**Problem 1:** https://atcoder.jp/contests/abc169/tasks/abc169_d
For every prime factor it is most optimal to greedy and take p^1, then p^2, then p^3, ... if possible. We can see how many we can take. Since 2^55 > 10^12, we know that the max we can take is smaller than p^10. Thus for every prime we just keep taking exponents p^1, p^2,... from the original p^e until we can't take anything(or else it will repeat) and we break. 
```cpp
vi prime; 
void sieve(int n){
	int v[n+1]={0};
	rep(k,2,n+1){
		if(v[k]){ 
			continue;
		}
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
void solve(){
    ll n;cin>>n;
    sieve(1000005);
    
    int ans = 0;
    rep(j,0,prime.size()){
        if(prime[j] > sqrt(n)){ // save more time
            break;
        }
        if(n%prime[j]==0){
            int cnt = 0;
            while(n%prime[j]==0){
                n/=prime[j];
                cnt++;
            }
            int sum = 0;
            rep(i,1,10){
                if(sum + i <= cnt){
                    sum+=i;
                    ans++;
                }
                else{
                    break;
                }
            }
        }
    }
    if(n>1){
        ans++;
    }
    cout<<ans<<endl;
}
```
**Problem 2:** https://codeforces.com/problemset/problem/1514/C
I couldnt get a 1600 T-T literally no idea how to do it lol.
Pre-observation 1: If $product \equiv 1 \pmod{k}$, then gcd(ans,k) = 1.
Observation 1: You can only have numbers coprime to n in the subsequence.
Proof: If there is a number in the subsequence such that gcd(a_i,n) > 1, then gcd(ans, n) > 1 since we multiple whatever else by a_i we get gcd(x * a_i,n) > 1.
Observation 2(**this was pretty smart imo**): We note that the product of all numbers coprime to n is also coprime to n since they will not share any factors in prime factored form. Thus, product mod n is coprime to n: 1 = gcd(product,n) = gcd(product mod n, n). Since this is the product of ALL numbers that are coprime to n, if this product mod n is not 1 we can just erase whichever number product mod n is since its included in the product(since its coprime to n).
Thus the solution is just: find product of all coprime of n then mod n, then if product mod n = 1, we print all coprime. If product mod n != 1, we print all coprime except for product mod n.
**Review: How to solve this when n <= 10^12?**
```cpp
void solve(){
    int n;cin>>n;
    ll prod = 1;
    vi v;
    rep(i,1,n){
        if(gcd(i,n)==1){ // logn
            prod=(prod*i)%n;
            v.pb(i);
        }
    }
    if(prod == 1){
        cout<<v.size()<<endl;
        rep(i,0,v.size()){
            cout<<v[i]<<" ";
        }
        cout<<endl;
    }
    else{
        cout<<v.size()-1<<endl;
        rep(i,0,v.size()){
            if(v[i] != prod){
                cout<<v[i]<<" ";
            }
            
        }
        cout<<endl;
    }
    
}
```
**Problem 3:** https://codeforces.com/contest/1225/problem/D
I immediately realized you can do this by for every x we find the number of inverse exponents so that their exponent sum is k. However here are several mistakes that costed me an hour to solve this:
1. I was scared of using map <vector>: Since there is only log(n) primes factors, the vector will actually be very small so its possible to not tle using map vector
2. I did vi instead of vector <pi> to store prime factors: Using vector <pi> will elminate all other prime factors that are not relavent, meaning instead of a size 500ish vector of all primes up to 10^5, you only get about a size < 50(log(n)) vector.
3. TLE since we checked more primes than we have to: We are checking all primes up to 10^5 in the original loop. In order to not TLE, we must only check up to sqrt(n) primes, the left over prime > sqrt(n) can be inserted into the vector <pi> as a prime with exponent 1.
```cpp
vi prime; 
void sieve(int n){
	int v[n+1]={0};
	rep(k,2,n+1){
		if(v[k]){ 
			continue;
		}
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
void solve(){
    int n,k;cin>>n>>k;
    sieve(100005);
    
    ll ans = 0;
    map <vector<pi>,int> mp;
    vi a;
    vector <pi> num[n];
    rep(i,0,n){
        int u;cin>>u;a.pb(u);   
        rep(j,0,prime.size()){
            if(prime[j] > sqrt(u)){ // save more time
                break;
            }
            if(u%prime[j]==0){
                int cnt = 0;
                while(u%prime[j]==0){
                    u/=prime[j];
                    cnt++;
                }
                if(cnt%k>0){ // we only insert things that might make an impact
                    // that is, things that are not 0 mod k
                    num[i].pb({cnt%k,prime[j]});
                }                
            }
        }
        if(u>1){
            num[i].pb({1,u});
            
        }
        mp[num[i]]++;
    }
    rep(i,0,n){
        bool yn = true;
        vector<pi> v;
        rep(j,0,num[i].size()){
            if(num[i][j].first != (k-num[i][j].first)%k){
                yn = false;
            }
            v.pb({(k-num[i][j].first)%k,num[i][j].second});
        }   
        if(yn){
            ans += mp[v]-1; // x * x = p^k, since we cannot choose i,j where i = j, we subtract 1 from the count map
        }
        else{
            ans += mp[v];
        }
        
    }
    cout<<ans/2<<endl;
}
```

**Problem 4:** 
We each prefix, we note that in order for all ratios of chunks to be the same, they must all have the ratio of the prefix. Thus, if we have ratio a/b in lowest term then the maximum chunk is just the previous maximum value with this ratio + 1 since we have increase some D and K by then so we can form exactly one new chunk.
```cpp
void solve(){
    int n;cin>>n;
    string s;cin>>s;
    int a = 0; int b = 0;
    map <pi,int> mx;
    rep(i,0,n){
        if(s[i] == 'D'){
            a++;
        }
        else{
            b++;
        }
        cout<<(mx[{a/gcd(a,b), b/gcd(a,b)}]++)+1<<" ";
    }
    cout<<endl;
    
}
```

**Problem 6:** https://codeforces.com/contest/1349/problem/A
We note that gcd and lcm are associative. 
Lets solve for every v_i: x_i = gcd(lcm of vi with every other number in v). Then the answer is just the gcd of all x_i since gcd is associative. Thus, x_i = gcd(lcm of all numbers to the right of it with v_i). We know that lcm(a,b) must be divisible by a. And thus lcm(v_i,a) will be divisible by v_i for all other number a. Thus the gcd of that must include v_i. We then know that the gcd takes the minimum power of lcm(v_i,a)/v_i for every a. Thus gcd will take all gcd(every other number) in order to find their smallest exponents for each thing. Since this gcd(every other number together) might also include some of v_i itself, we must lcm or smth idk. **Anyway still kinda confused on what i did I just tried stuff and it worked so... BUT I DONT KNOW A CONCRETE CONVINCING MATH PROOF YK**

```cpp
void solve(){
    int n;cin>>n;
    ll suf[n];
    suf[n-1]=0;
    vector<ll> v;
    rep(i,0,n){
        int u;cin>>u; v.pb(u);
        
    }
    for(int i = n-2;i>=0;i--){
        suf[i] = gcd(suf[i+1], v[i+1]);
    }
    ll ans = 0;
    rep(i,0,n){
        ans = gcd(ans, lcm(v[i], suf[i]));
    }
    
    cout<<ans<<endl;
    
}
```
