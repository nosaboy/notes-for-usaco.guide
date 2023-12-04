A graph where the outdegree of each node is exactly 1(each node goes to exactly one other node). 
We can thus define f(x,k) as the node we will reach from x and walking k steps. Since each step there is exactly one option, f(x,k) always produces some other node.

With naive approach of simulation, we can query f(x,k) in O(k) time. With smart preprocessing we can query f(x,k) in O(logk) time.

For each node x, lets precalculate all values f(x,k) where k < # of noes and **k is a power of 2**. After this, we know that any number k is made up of powers of 2(in binary representation) so we can just recursively go over each bit. Let the pth bit of k be a 1 and we are starting at x. We can then jump from x to f(x,2^p), so we immediately took 2^p steps. We then set f(x,2^p) as our new starting point. Calculating f(x,k) for any k thus becomes summing up the powers of 2, so f(x,11) = f(f(x,8),3) = f(f(f(x,8),2),1) which takes log(k) time.

To pre calculate each f(x,k), we use the following recursion for all powers of two k:
- if k = 1, f(x,k) = $go[x]$ where go denotes the node x points to.
- if k > 1, f(x,k) = f(f(x,k/2),k/2): Assuming that k/2 is calculated, we go k/2 steps from x and arrive at f(x,k/2), then take another k/2 steps to arrive at f(x,k). We do this for every node x and for each node x we only have logu numbers that we want to calculate, so the time is O(nlogu) where u is the maximum length of a jump.





  
