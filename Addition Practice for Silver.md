**Problem 2:** https://codeforces.com/problemset/problem/1359/C
Since the temperature is the average of the cups, we know that the temperature can be 2 choices at any given time. If the optimal time $n$ is even, we know that we have poured n/2 cups of hot water and n/2 cups
of cold water so the average is (h * n/2 + c * n/2)/n = (n(h+c)/2)/n = (h+c)/2. If $n$ is odd, we know that since we poured 1 cup of hot water first, we poured 1 cup of hot water last. Thus, the average is
(h * (n-1)/2 + c * (n-1)/2 + h)/n = (n-1)/n * (h+c)/2 + h/n.

We know that if days are even, then the avg temperature is always (x+y)/2. If the days are odd, the avg temperature is always (d(x+y) + x)/(2d+1) = 


