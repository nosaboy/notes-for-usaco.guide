## Usage in Tree Queries:
- Find kth ancestor
- Find LCA of two nodes
- Find distance of two nodes.


### Kth Ancestor Query in Logn

```cpp
const int MX = 2e5 + 5;  // maximum N
const int MS = 18;       // 18 is calculated by $\log_2 2e5$

int up[MS][MX]; // = [2^i levels above node][node]

int jmp(int x, int d) { // QUERY return kth ancestor
	rep(i,0,MS) if ((d >> i) & 1) x = up[i][x];
	return x ?: -1;  // modfication to return -1 if not found
}

void init(int n){ // n nodes
  // precalc all nodes 2^0 = 1 level above it
  // we set up[0][1] = " " as 1 is root and thus there are no
  rep(i, 2, n + 1) {
		int a;
		cin >> a; // a is the parent of node i
		up[0][i] = a; // a is 1 level above i
	}

	rep(i, 1, MS){ // 2^i nodes above current node
    rep(j, 1, n + 1){ // current node
      up[i][j] = up[i - 1][up[i - 1][j]];  // construct our parent array
    }
  }
	
	

	// uncomment below if you want to see the parent array
  /*
  rep(i, 0, MS) {
	     rep(j, 1, n+1) {
	         cerr << setfill(' ') << setw(2) << up[i][j] << " ";
	     }
	     cerr << "\n";
	}
  */


}

```

### LCA

```cpp
int depth[200005];
int up[200005][20]; // node, level 2^i
vi adj[200005];

void dfs(int v) {
	rep(i, 1, 20) { // for curr node, get all up values from previous values.
    up[v][i] = up[up[v][i - 1]][i - 1];
  }
  // visit all nodes downwards if not visited( not parent)
	for (int x : adj[v]) {
		if (x != up[v][0]) {
			depth[x] = depth[up[x][0] = v] + 1;
			dfs(x);
		}
	}
}

int jump(int x, int d) { // d levels above
	rep(i,0,20) {
		if ((d >> i) & 1) x = up[x][i];
	}
	return x;
}

int LCA(int a, int b) {
	if (depth[a] < depth[b]) swap(a, b);

	a = jump(a, depth[a] - depth[b]);
	if (a == b) return a;

	for(int i = 19;i>=0;i--) {
		int aT = up[a][i], bT = up[b][i];
		if (aT != bT) a = aT, b = bT;
	}

	return up[a][0];
}

```

