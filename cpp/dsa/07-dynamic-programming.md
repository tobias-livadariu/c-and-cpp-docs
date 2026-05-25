# Dynamic Programming

## Learning Objectives

You should be able to:

- Recognize overlapping subproblems and optimal substructure.
- Define DP state and transition.
- Choose memoization or tabulation.
- Implement common 1D, 2D, knapsack, LIS, grid, and bitmask DP patterns.

## DP Design Checklist

1. State: what does `dp[...]` mean?
2. Transition: how does a larger state use smaller states?
3. Base cases: what is known without recursion?
4. Order: are dependencies computed before use?
5. Answer: which state contains the final result?
6. Complexity: how many states times work per state?

If you cannot write the state meaning in one sentence, do not code yet.

## Memoization

Top-down recursion plus cache.

```cpp
int fib(int n, vector<int>& memo) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    return memo[n] = fib(n - 1, memo) + fib(n - 2, memo);
}
```

Use when recursion matches the problem naturally.

## Tabulation

Bottom-up iteration.

```cpp
vector<int> dp(n + 1);
dp[0] = 0;
dp[1] = 1;

for (int i = 2; i <= n; ++i) {
    dp[i] = dp[i - 1] + dp[i - 2];
}
```

Use when order is easy and recursion depth may be risky.

## 1D DP: Climbing Stairs

State: `dp[i]` is the number of ways to reach step `i`.

```cpp
int climb_stairs(int n) {
    vector<int> dp(n + 1);
    dp[0] = 1;

    for (int i = 1; i <= n; ++i) {
        dp[i] += dp[i - 1];
        if (i >= 2) dp[i] += dp[i - 2];
    }
    return dp[n];
}
```

## Grid DP

State: `dp[r][c]` is the best answer to reach cell `(r, c)`.

```cpp
int min_path_sum(vector<vector<int>>& grid) {
    int rows = static_cast<int>(grid.size());
    int cols = static_cast<int>(grid[0].size());
    vector<vector<int>> dp(rows, vector<int>(cols, INT_MAX));

    dp[0][0] = grid[0][0];
    for (int r = 0; r < rows; ++r) {
        for (int c = 0; c < cols; ++c) {
            if (r > 0) dp[r][c] = min(dp[r][c], dp[r - 1][c] + grid[r][c]);
            if (c > 0) dp[r][c] = min(dp[r][c], dp[r][c - 1] + grid[r][c]);
        }
    }
    return dp[rows - 1][cols - 1];
}
```

## 0/1 Knapsack

State: `dp[w]` is best value with capacity `w` after processed items.

```cpp
int knapsack(const vector<int>& weight, const vector<int>& value, int cap) {
    vector<int> dp(cap + 1);

    for (int i = 0; i < static_cast<int>(weight.size()); ++i) {
        for (int w = cap; w >= weight[i]; --w) {
            dp[w] = max(dp[w], dp[w - weight[i]] + value[i]);
        }
    }
    return dp[cap];
}
```

Loop capacity backward so each item is used at most once.

## Unbounded Knapsack

```cpp
for (int i = 0; i < n; ++i) {
    for (int w = weight[i]; w <= cap; ++w) {
        dp[w] = max(dp[w], dp[w - weight[i]] + value[i]);
    }
}
```

Loop capacity forward so the same item can be reused.

## Longest Increasing Subsequence

`O(n log n)` tails method:

```cpp
int lis_length(const vector<int>& a) {
    vector<int> tails;
    for (int x : a) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) tails.push_back(x);
        else *it = x;
    }
    return static_cast<int>(tails.size());
}
```

`tails[len - 1]` is the smallest possible tail of an increasing subsequence of length `len`.

Use `upper_bound` instead of `lower_bound` for non-decreasing subsequences.

## Interval DP

State often looks like `dp[l][r]`, answer for subarray `l..r`.

```cpp
for (int len = 1; len <= n; ++len) {
    for (int l = 0; l + len <= n; ++l) {
        int r = l + len - 1;
        // compute dp[l][r] from smaller intervals
    }
}
```

Common in palindrome, matrix chain, burst balloons, and game DP.

## Bitmask DP

Use when `n` is small, often `n <= 20`.

```cpp
vector<int> dp(1 << n, INF);
dp[0] = 0;

for (int mask = 0; mask < (1 << n); ++mask) {
    for (int i = 0; i < n; ++i) {
        if (mask & (1 << i)) continue;
        int next = mask | (1 << i);
        dp[next] = min(dp[next], dp[mask] + cost[mask][i]);
    }
}
```

## DAG DP

Topological order turns graph dependencies into DP order.

```cpp
for (int u : topo) {
    for (int v : graph[u]) {
        dp[v] = max(dp[v], dp[u] + 1);
    }
}
```

## Common Pitfalls

- Coding before defining state.
- Wrong iteration direction in knapsack.
- Forgetting base cases.
- Using `int` when counts or costs overflow.
- Recursion depth in top-down DP.
- Recomputing transitions that can be optimized by prefix sums, monotonic queues, or convex hull tricks.

## Self-Check

1. What does your `dp` state mean?
2. Why does 0/1 knapsack iterate capacity backward?
3. What is the difference between LIS with `lower_bound` and `upper_bound`?
4. When is bitmask DP feasible?
