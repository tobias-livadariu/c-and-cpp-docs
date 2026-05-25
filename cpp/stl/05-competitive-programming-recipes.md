# Competitive Programming STL Recipes

## Learning Objectives

You should be able to:

- Recall common STL snippets without searching.
- Combine containers and algorithms into standard DSA building blocks.
- Recognize when STL solves a problem pattern directly.

## Standard Template

```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    return 0;
}
```

## Sort And Deduplicate

```cpp
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

## Coordinate Compression

```cpp
vector<int> xs = a;
sort(xs.begin(), xs.end());
xs.erase(unique(xs.begin(), xs.end()), xs.end());

for (int& x : a) {
    x = static_cast<int>(lower_bound(xs.begin(), xs.end(), x) - xs.begin());
}
```

## Frequency Count

```cpp
unordered_map<int, int> freq;
freq.reserve(2 * n);

for (int x : a) {
    ++freq[x];
}
```

For small alphabet:

```cpp
array<int, 26> cnt{};
for (char c : s) ++cnt[c - 'a'];
```

## Prefix Sum

```cpp
vector<long long> pref(n + 1);
for (int i = 0; i < n; ++i) {
    pref[i + 1] = pref[i] + a[i];
}

auto range_sum = [&](int l, int r) {
    return pref[r] - pref[l];  // [l, r)
};
```

## Difference Array

```cpp
vector<long long> diff(n + 1);

// add x to [l, r]
diff[l] += x;
if (r + 1 < n) diff[r + 1] -= x;

for (int i = 1; i < n; ++i) {
    diff[i] += diff[i - 1];
}
```

For half-open `[l, r)`, use:

```cpp
diff[l] += x;
diff[r] -= x;
```

## Top K With Heap

```cpp
priority_queue<int, vector<int>, greater<int>> pq;

for (int x : a) {
    pq.push(x);
    if (static_cast<int>(pq.size()) > k) pq.pop();
}
```

At the end, `pq` contains the largest `k` values, with the smallest of them at top.

## Dijkstra Heap Entry

```cpp
using State = pair<long long, int>; // distance, node
priority_queue<State, vector<State>, greater<State>> pq;
```

Pairs give the right ordering automatically.

## Sliding Window Maximum

```cpp
deque<int> dq; // stores indices, values decreasing
vector<int> ans;

for (int r = 0; r < n; ++r) {
    while (!dq.empty() && a[dq.back()] <= a[r]) dq.pop_back();
    dq.push_back(r);

    int l = r - k + 1;
    if (dq.front() < l) dq.pop_front();

    if (l >= 0) ans.push_back(a[dq.front()]);
}
```

## Ordered Set Neighbor Query

```cpp
set<int> s;

auto it = s.lower_bound(x);
if (it != s.end()) {
    int succ = *it;
}
if (it != s.begin()) {
    int pred = *prev(it);
}
```

## Multiset Window Median Skeleton

```cpp
multiset<int> lo, hi; // lo has smaller half, hi has larger half

auto rebalance = [&]() {
    while (lo.size() > hi.size() + 1) {
        hi.insert(*prev(lo.end()));
        lo.erase(prev(lo.end()));
    }
    while (hi.size() > lo.size()) {
        lo.insert(*hi.begin());
        hi.erase(hi.begin());
    }
};
```

## Custom Sort For Intervals

```cpp
sort(intervals.begin(), intervals.end(), [](const auto& a, const auto& b) {
    if (a[0] != b[0]) return a[0] < b[0];
    return a[1] < b[1];
});
```

If using `vector<vector<int>>`, indexing is common. If you control the type, prefer a struct or pair.

## Grid Directions

```cpp
const int dr[4] = {-1, 0, 1, 0};
const int dc[4] = {0, 1, 0, -1};

for (int k = 0; k < 4; ++k) {
    int nr = r + dr[k];
    int nc = c + dc[k];
    if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) continue;
}
```

## Graph Adjacency

Unweighted:

```cpp
vector<vector<int>> graph(n);
graph[u].push_back(v);
graph[v].push_back(u);
```

Weighted:

```cpp
vector<vector<pair<int, int>>> graph(n); // to, weight
graph[u].push_back({v, w});
```

## Erasing While Iterating

For `set`/`map`:

```cpp
for (auto it = s.begin(); it != s.end(); ) {
    if (should_remove(*it)) it = s.erase(it);
    else ++it;
}
```

For `vector`, use erase-remove:

```cpp
v.erase(remove_if(v.begin(), v.end(), pred), v.end());
```

## Self-Check

1. Why does coordinate compression need `sort` and `unique`?
2. Why does the monotonic deque store indices instead of values?
3. What does a min-heap declaration look like?
4. When does `map[key]` create a bug?
