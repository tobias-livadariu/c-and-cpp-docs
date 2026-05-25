# Shortest Paths, MST, And Union-Find

## Learning Objectives

You should be able to:

- Use Dijkstra for nonnegative weighted shortest paths.
- Use 0-1 BFS for weights `0` and `1`.
- Recognize when Bellman-Ford or Floyd-Warshall applies.
- Use DSU for connectivity and Kruskal's MST.

## Algorithm Choice

| Problem | Algorithm |
| --- | --- |
| Unweighted shortest path | BFS |
| Edge weights 0 or 1 | 0-1 BFS |
| Nonnegative weights | Dijkstra |
| Negative edges, no huge constraints | Bellman-Ford |
| All-pairs, small `n` | Floyd-Warshall |
| Minimum spanning tree | Kruskal or Prim |
| Dynamic connectivity with unions | DSU |

## Dijkstra

Use when all edge weights are nonnegative.

```cpp
vector<long long> dijkstra(const vector<vector<pair<int, int>>>& graph, int src) {
    const long long INF = numeric_limits<long long>::max() / 4;
    int n = static_cast<int>(graph.size());

    vector<long long> dist(n, INF);
    using State = pair<long long, int>; // distance, node
    priority_queue<State, vector<State>, greater<State>> pq;

    dist[src] = 0;
    pq.push({0, src});

    while (!pq.empty()) {
        auto [d, u] = pq.top();
        pq.pop();

        if (d != dist[u]) continue; // stale entry

        for (auto [v, w] : graph[u]) {
            if (d + w < dist[v]) {
                dist[v] = d + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

Invariant: when a node is popped with `d == dist[u]`, that distance is final.

## 0-1 BFS

Use when weights are only `0` or `1`.

```cpp
vector<int> zero_one_bfs(const vector<vector<pair<int, int>>>& graph, int src) {
    const int INF = 1e9;
    int n = static_cast<int>(graph.size());

    vector<int> dist(n, INF);
    deque<int> dq;

    dist[src] = 0;
    dq.push_front(src);

    while (!dq.empty()) {
        int u = dq.front();
        dq.pop_front();

        for (auto [v, w] : graph[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                if (w == 0) dq.push_front(v);
                else dq.push_back(v);
            }
        }
    }
    return dist;
}
```

The deque keeps near-smaller distances at the front.

## Bellman-Ford

Use for negative edges or negative cycle detection with moderate constraints.

```cpp
struct Edge {
    int u;
    int v;
    long long w;
};

vector<long long> bellman_ford(int n, const vector<Edge>& edges, int src) {
    const long long INF = numeric_limits<long long>::max() / 4;
    vector<long long> dist(n, INF);
    dist[src] = 0;

    for (int iter = 0; iter < n - 1; ++iter) {
        bool changed = false;
        for (auto [u, v, w] : edges) {
            if (dist[u] == INF) continue;
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                changed = true;
            }
        }
        if (!changed) break;
    }
    return dist;
}
```

One more relaxation pass detects a reachable negative cycle.

## Floyd-Warshall

All-pairs shortest paths for small `n`, usually `n <= 400` depending on constraints.

```cpp
for (int k = 0; k < n; ++k) {
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < n; ++j) {
            dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
        }
    }
}
```

Guard against `INF + x` overflow if using large sentinels.

## DSU

```cpp
struct DSU {
    vector<int> parent;
    vector<int> sz;

    explicit DSU(int n) : parent(n), sz(n, 1) {
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }

    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;
        if (sz[a] < sz[b]) swap(a, b);
        parent[b] = a;
        sz[a] += sz[b];
        return true;
    }
};
```

Path compression plus union by size/rank makes operations effectively constant for DSA constraints.

## Kruskal MST

```cpp
struct Edge {
    int u;
    int v;
    int w;
};

long long kruskal(int n, vector<Edge> edges) {
    sort(edges.begin(), edges.end(), [](const Edge& a, const Edge& b) {
        return a.w < b.w;
    });

    DSU dsu(n);
    long long cost = 0;
    int used = 0;

    for (const Edge& e : edges) {
        if (dsu.unite(e.u, e.v)) {
            cost += e.w;
            ++used;
        }
    }

    if (used != n - 1) return -1; // disconnected
    return cost;
}
```

Greedy choice: the smallest edge connecting two different components is safe.

## Common Pitfalls

- Running Dijkstra with negative weights.
- Forgetting stale-entry skip in Dijkstra.
- Using `int` for distances.
- Not checking disconnected MST.
- Treating directed and undirected edges the same.
- Forgetting Floyd-Warshall is `O(n^3)`.

## Self-Check

1. When is Dijkstra invalid?
2. Why does the priority queue version need stale-entry checks?
3. What are path compression and union by size doing?
4. Why does Kruskal sort edges first?
