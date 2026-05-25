# Stacks, Queues, Heaps, And Monotonic Structures

## Learning Objectives

You should be able to:

- Use stacks for nesting, backtracking, and next greater/smaller patterns.
- Use queues for BFS.
- Use heaps for top-k, scheduling, and shortest-path frontiers.
- Use monotonic stacks and deques.

## Recognition Signals

- "Next greater", "previous smaller", or "span" suggests monotonic stack.
- "Level order" or "minimum number of unweighted steps" suggests BFS queue.
- "Always process smallest/largest next" suggests heap.
- "Sliding window maximum/minimum" suggests monotonic deque.
- Parentheses or nested intervals suggest stack.

## Stack Basics

```cpp
stack<int> st;
st.push(1);
int x = st.top();
st.pop();
```

If you need iteration, use `vector<int>` as a stack:

```cpp
vector<int> st;
st.push_back(x);
st.pop_back();
```

## Valid Parentheses

```cpp
bool valid(const string& s) {
    stack<char> st;

    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char o = st.top();
            st.pop();
            if ((c == ')' && o != '(') ||
                (c == ']' && o != '[') ||
                (c == '}' && o != '{')) {
                return false;
            }
        }
    }
    return st.empty();
}
```

Invariant: stack contains unmatched open brackets in order.

## Monotonic Stack: Next Greater Element

```cpp
vector<int> next_greater(const vector<int>& a) {
    int n = static_cast<int>(a.size());
    vector<int> ans(n, -1);
    vector<int> st; // indices, values decreasing

    for (int i = 0; i < n; ++i) {
        while (!st.empty() && a[st.back()] < a[i]) {
            ans[st.back()] = a[i];
            st.pop_back();
        }
        st.push_back(i);
    }
    return ans;
}
```

Invariant: indices in `st` have decreasing values and have not found their next greater value yet.

## Monotonic Stack: Largest Rectangle In Histogram

```cpp
int largest_rectangle(vector<int> h) {
    h.push_back(0);
    vector<int> st;
    int best = 0;

    for (int i = 0; i < static_cast<int>(h.size()); ++i) {
        while (!st.empty() && h[st.back()] > h[i]) {
            int height = h[st.back()];
            st.pop_back();
            int left = st.empty() ? -1 : st.back();
            int width = i - left - 1;
            best = max(best, height * width);
        }
        st.push_back(i);
    }
    return best;
}
```

The sentinel `0` flushes remaining bars.

## Queue For BFS

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

Invariant: nodes leave the queue in nondecreasing distance order for unweighted graphs.

## Monotonic Deque: Sliding Window Maximum

```cpp
vector<int> max_sliding_window(const vector<int>& a, int k) {
    deque<int> dq; // indices, values decreasing
    vector<int> ans;

    for (int r = 0; r < static_cast<int>(a.size()); ++r) {
        while (!dq.empty() && a[dq.back()] <= a[r]) dq.pop_back();
        dq.push_back(r);

        int l = r - k + 1;
        while (!dq.empty() && dq.front() < l) dq.pop_front();

        if (l >= 0) ans.push_back(a[dq.front()]);
    }
    return ans;
}
```

Invariant: deque front is the maximum value index for the current window.

## Heap Basics

Max-heap:

```cpp
priority_queue<int> pq;
```

Min-heap:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

Top-k largest:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
for (int x : a) {
    pq.push(x);
    if (static_cast<int>(pq.size()) > k) pq.pop();
}
```

## Two Heaps For Median

```cpp
priority_queue<int> lo;
priority_queue<int, vector<int>, greater<int>> hi;

auto add = [&](int x) {
    if (lo.empty() || x <= lo.top()) lo.push(x);
    else hi.push(x);

    if (lo.size() > hi.size() + 1) {
        hi.push(lo.top());
        lo.pop();
    } else if (hi.size() > lo.size()) {
        lo.push(hi.top());
        hi.pop();
    }
};
```

Invariant: every value in `lo` is `<=` every value in `hi`, and sizes differ by at most one.

## Common Pitfalls

- Calling `top`, `front`, or `back` on an empty container.
- Forgetting `priority_queue` is max-heap by default.
- Storing values instead of indices in a monotonic deque when expiration matters.
- Using BFS on weighted graphs.
- Forgetting stale-entry checks in heap-based algorithms.

## Self-Check

1. What invariant does a monotonic stack maintain?
2. Why does BFS find shortest paths only in unweighted graphs?
3. How do you declare a min-heap?
4. Why does sliding window maximum store indices?
