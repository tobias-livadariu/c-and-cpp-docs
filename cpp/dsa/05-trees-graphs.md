# Trees And Graphs

## Learning Objectives

You should be able to:

- Traverse trees recursively and iteratively.
- Represent graphs with adjacency lists.
- Use DFS and BFS correctly.
- Detect cycles, connected components, and topological order.
- Handle grid graphs.

## Tree Node Pattern

LeetCode commonly provides:

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
};
```

Recursive DFS:

```cpp
int height(TreeNode* root) {
    if (root == nullptr) return 0;
    return 1 + max(height(root->left), height(root->right));
}
```

## Tree Traversals

```cpp
void inorder(TreeNode* root, vector<int>& out) {
    if (!root) return;
    inorder(root->left, out);
    out.push_back(root->val);
    inorder(root->right, out);
}
```

BST invariant: inorder traversal is sorted if the tree is a valid BST.

## Level Order BFS

```cpp
vector<vector<int>> level_order(TreeNode* root) {
    vector<vector<int>> ans;
    if (!root) return ans;

    queue<TreeNode*> q;
    q.push(root);

    while (!q.empty()) {
        int sz = static_cast<int>(q.size());
        vector<int> level;

        for (int i = 0; i < sz; ++i) {
            TreeNode* node = q.front();
            q.pop();
            level.push_back(node->val);
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        ans.push_back(level);
    }
    return ans;
}
```

## Graph Representation

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

Use 0-based indexing internally when possible.

## DFS Components

```cpp
vector<int> seen(n);

function<void(int)> dfs = [&](int u) {
    seen[u] = 1;
    for (int v : graph[u]) {
        if (!seen[v]) dfs(v);
    }
};

int components = 0;
for (int i = 0; i < n; ++i) {
    if (!seen[i]) {
        ++components;
        dfs(i);
    }
}
```

For very deep graphs, use iterative DFS to avoid stack overflow.

## BFS Shortest Path In Unweighted Graph

```cpp
vector<int> dist(n, -1);
queue<int> q;

dist[start] = 0;
q.push(start);

while (!q.empty()) {
    int u = q.front();
    q.pop();

    for (int v : graph[u]) {
        if (dist[v] != -1) continue;
        dist[v] = dist[u] + 1;
        q.push(v);
    }
}
```

## Cycle Detection In Directed Graph

Colors:

- `0` unvisited,
- `1` visiting,
- `2` done.

```cpp
vector<int> color(n);

function<bool(int)> has_cycle = [&](int u) {
    color[u] = 1;
    for (int v : graph[u]) {
        if (color[v] == 1) return true;
        if (color[v] == 0 && has_cycle(v)) return true;
    }
    color[u] = 2;
    return false;
};
```

Back edge to a visiting node means directed cycle.

## Topological Sort

Kahn's algorithm:

```cpp
vector<int> indeg(n);
for (int u = 0; u < n; ++u) {
    for (int v : graph[u]) ++indeg[v];
}

queue<int> q;
for (int i = 0; i < n; ++i) {
    if (indeg[i] == 0) q.push(i);
}

vector<int> order;
while (!q.empty()) {
    int u = q.front();
    q.pop();
    order.push_back(u);

    for (int v : graph[u]) {
        if (--indeg[v] == 0) q.push(v);
    }
}

bool acyclic = static_cast<int>(order.size()) == n;
```

Use for prerequisite scheduling and DAG DP.

## Grid Graphs

```cpp
const int dr[4] = {-1, 0, 1, 0};
const int dc[4] = {0, 1, 0, -1};

for (int k = 0; k < 4; ++k) {
    int nr = r + dr[k];
    int nc = c + dc[k];
    if (nr < 0 || nr >= rows || nc < 0 || nc >= cols) continue;
}
```

Grid BFS:

```cpp
queue<pair<int, int>> q;
vector<vector<int>> dist(rows, vector<int>(cols, -1));
```

## Common Pitfalls

- Recursive DFS stack overflow on long chains.
- Forgetting to mark visited before pushing/enqueuing, causing duplicates.
- Using BFS for weighted shortest paths.
- Confusing directed and undirected cycle detection.
- Not handling disconnected graphs.
- Off-by-one errors when converting from 1-based input.

## Self-Check

1. Why does BFS give shortest path length in unweighted graphs?
2. What do directed DFS colors mean?
3. How does Kahn's algorithm detect a cycle?
4. Why might recursive DFS fail on `2e5` nodes?
