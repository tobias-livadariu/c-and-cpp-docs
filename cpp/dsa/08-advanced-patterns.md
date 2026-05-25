# Advanced Patterns

## Learning Objectives

You should be able to:

- Recognize when common advanced structures apply.
- Implement reusable templates for Fenwick tree, segment tree, trie, sparse table, and backtracking.
- Understand the role of sweep line, SCC, and LCA patterns.

## Backtracking

Use when exploring combinations, permutations, subsets, or constraint assignments.

```cpp
void backtrack(int i, vector<int>& cur, const vector<int>& nums, vector<vector<int>>& ans) {
    if (i == static_cast<int>(nums.size())) {
        ans.push_back(cur);
        return;
    }

    backtrack(i + 1, cur, nums, ans);

    cur.push_back(nums[i]);
    backtrack(i + 1, cur, nums, ans);
    cur.pop_back();
}
```

Invariant: `cur` represents choices made for indices before `i`.

## Trie

Use for prefix queries over strings or bits.

```cpp
struct Trie {
    struct Node {
        array<int, 26> next;
        bool end = false;

        Node() {
            next.fill(-1);
        }
    };

    vector<Node> nodes = {Node()};

    void insert(const string& s) {
        int u = 0;
        for (char c : s) {
            int k = c - 'a';
            if (nodes[u].next[k] == -1) {
                nodes[u].next[k] = static_cast<int>(nodes.size());
                nodes.push_back(Node());
            }
            u = nodes[u].next[k];
        }
        nodes[u].end = true;
    }

    bool search(const string& s) const {
        int u = 0;
        for (char c : s) {
            int k = c - 'a';
            if (nodes[u].next[k] == -1) return false;
            u = nodes[u].next[k];
        }
        return nodes[u].end;
    }
};
```

## Fenwick Tree

Use for point updates and prefix/range sum queries.

```cpp
struct Fenwick {
    int n;
    vector<long long> bit;

    explicit Fenwick(int n) : n(n), bit(n + 1) {}

    void add(int idx, long long delta) {
        for (++idx; idx <= n; idx += idx & -idx) {
            bit[idx] += delta;
        }
    }

    long long sum_prefix(int idx) const {
        long long ans = 0;
        for (++idx; idx > 0; idx -= idx & -idx) {
            ans += bit[idx];
        }
        return ans;
    }

    long long sum_range(int l, int r) const {
        if (r <= l) return 0;
        return sum_prefix(r - 1) - (l == 0 ? 0 : sum_prefix(l - 1));
    }
};
```

Use coordinate compression when indices are large.

## Segment Tree

Use for range queries plus updates when Fenwick is not expressive enough.

```cpp
struct SegTree {
    int n = 1;
    vector<long long> tree;

    explicit SegTree(int size) {
        while (n < size) n *= 2;
        tree.assign(2 * n, 0);
    }

    void set_value(int idx, long long value) {
        idx += n;
        tree[idx] = value;
        for (idx /= 2; idx >= 1; idx /= 2) {
            tree[idx] = tree[2 * idx] + tree[2 * idx + 1];
        }
    }

    long long query(int l, int r) const {
        long long ans = 0;
        l += n;
        r += n;
        while (l < r) {
            if (l % 2 == 1) ans += tree[l++];
            if (r % 2 == 1) ans += tree[--r];
            l /= 2;
            r /= 2;
        }
        return ans;
    }
};
```

This example is range sum on `[l, r)`. Change the combine operation for min/max/gcd.

## Sparse Table

Use for static idempotent range queries, especially range minimum query.

```cpp
struct SparseTable {
    vector<vector<int>> st;
    vector<int> lg;

    explicit SparseTable(const vector<int>& a) {
        int n = static_cast<int>(a.size());
        lg.assign(n + 1, 0);
        for (int i = 2; i <= n; ++i) lg[i] = lg[i / 2] + 1;

        st.assign(lg[n] + 1, vector<int>(n));
        st[0] = a;

        for (int k = 1; k < static_cast<int>(st.size()); ++k) {
            for (int i = 0; i + (1 << k) <= n; ++i) {
                st[k][i] = min(st[k - 1][i], st[k - 1][i + (1 << (k - 1))]);
            }
        }
    }

    int query_min(int l, int r) const {
        int len = r - l;
        int k = lg[len];
        return min(st[k][l], st[k][r - (1 << k)]);
    }
};
```

Query is `[l, r)`. Works for idempotent operations like min, max, and gcd.

## Sweep Line

Use for interval overlap, active events, and geometry-like ordered scans.

```cpp
vector<pair<int, int>> events;
for (auto [l, r] : intervals) {
    events.push_back({l, +1});
    events.push_back({r, -1});
}

sort(events.begin(), events.end());

int active = 0;
int best = 0;
for (auto [x, delta] : events) {
    active += delta;
    best = max(best, active);
}
```

Tie handling matters. Decide whether intervals are closed `[l, r]` or half-open `[l, r)`.

## Strongly Connected Components

Use SCC when directed graph nodes form mutually reachable groups.

Common algorithms:

- Kosaraju: DFS order, reverse graph DFS.
- Tarjan: one DFS with low-link values.

Recognition:

- "If A reaches B and B reaches A."
- Condense directed graph into a DAG.
- 2-SAT uses SCC.

## LCA With Binary Lifting

Use for repeated ancestor/path queries on a static tree.

Precompute `up[k][v]`: the `2^k` ancestor of `v`.

```cpp
for (int k = 1; k < LOG; ++k) {
    for (int v = 0; v < n; ++v) {
        up[k][v] = up[k - 1][up[k - 1][v]];
    }
}
```

Lift deeper node first, then lift both nodes together.

## Common Pitfalls

- Fenwick indices off by one.
- Segment tree query range mismatch between `[l, r)` and `[l, r]`.
- Sparse table used for non-idempotent sum queries with overlapping ranges.
- Trie memory blowups for large alphabets.
- Sweep line tie rules not matching interval semantics.
- Backtracking missing `pop_back`, leaving polluted state.

## Self-Check

1. What operations does Fenwick support well?
2. When is a segment tree better than a Fenwick tree?
3. Why does sparse table RMQ work with two overlapping intervals?
4. What state must be undone in backtracking?
