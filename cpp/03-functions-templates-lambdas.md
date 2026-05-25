# Functions, Templates, And Lambdas

## Learning Objectives

You should be able to:

- Write functions with correct parameter passing.
- Use overloads and default arguments conservatively.
- Read and write basic function and class templates.
- Use lambdas for sorting, searching, traversal, and callbacks.
- Build custom comparators for STL containers and algorithms.

## Function Parameters

Use the cheapest correct parameter form.

```cpp
int square(int x);                         // small value
long long sum(const vector<int>& nums);    // large read-only object
void sort_in_place(vector<int>& nums);     // mutable object
```

Avoid this unless you intentionally need a copy:

```cpp
long long sum(vector<int> nums);  // copies the whole vector
```

## Return Values

Returning by value is normal.

```cpp
vector<int> prefix_sums(const vector<int>& a) {
    vector<int> pref(a.size() + 1);
    for (int i = 0; i < static_cast<int>(a.size()); ++i) {
        pref[i + 1] = pref[i] + a[i];
    }
    return pref;
}
```

Do not return references to local variables.

## Overloads And Defaults

Overload when the operations are the same idea for different types:

```cpp
int area(int side) {
    return side * side;
}

int area(int w, int h) {
    return w * h;
}
```

Default arguments can simplify APIs:

```cpp
void dfs(int u, int parent = -1);
```

Avoid complicated overload sets in contest code. Simple helpers are easier to debug under time pressure.

## Templates

A function template works for multiple types.

```cpp
template <class T>
T clamp_value(T x, T lo, T hi) {
    return max(lo, min(x, hi));
}
```

A class template stores a type parameter:

```cpp
template <class T>
struct Fenwick {
    int n;
    vector<T> bit;

    explicit Fenwick(int n) : n(n), bit(n + 1) {}

    void add(int idx, T delta) {
        for (++idx; idx <= n; idx += idx & -idx) bit[idx] += delta;
    }

    T sum_prefix(int idx) const {
        T ans{};
        for (++idx; idx > 0; idx -= idx & -idx) ans += bit[idx];
        return ans;
    }
};
```

For LeetCode, you mostly consume templates through STL containers such as `vector<int>`, `priority_queue<pair<int,int>>`, and `unordered_map<string,int>`.

## Lambdas

A lambda is an inline function object.

```cpp
auto twice = [](int x) {
    return 2 * x;
};

cout << twice(21) << '\n';
```

Capture external variables:

```cpp
int target = 10;
auto close_to_target = [target](int x) {
    return abs(x - target);
};
```

Capture by reference when you intentionally mutate or observe changing state:

```cpp
int calls = 0;
auto visit = [&calls](int u) {
    ++calls;
    cout << u << '\n';
};
```

Default captures:

```cpp
[&]  // capture used variables by reference
[=]  // capture used variables by value
```

In short contest snippets `[&]` is common. In longer code, explicit captures are safer.

## Sorting With Comparators

Sort ascending:

```cpp
sort(a.begin(), a.end());
```

Sort descending:

```cpp
sort(a.begin(), a.end(), greater<int>());
```

Sort pairs by second, then first:

```cpp
sort(v.begin(), v.end(), [](const auto& lhs, const auto& rhs) {
    if (lhs.second != rhs.second) return lhs.second < rhs.second;
    return lhs.first < rhs.first;
});
```

Comparator rule: return `true` if `lhs` should come before `rhs`. It must define a strict weak ordering. Do not use `<=`.

Wrong:

```cpp
sort(a.begin(), a.end(), [](int x, int y) {
    return x <= y;  // wrong
});
```

## Lambdas For Recursion

C++17 lambdas cannot directly name themselves without help.

Simple option using `std::function`:

```cpp
function<int(int)> fib = [&](int n) -> int {
    if (n <= 1) return n;
    return fib(n - 1) + fib(n - 2);
};
```

For performance-sensitive recursive lambdas, pass self:

```cpp
auto dfs = [&](auto&& self, int u, int p) -> void {
    for (int v : graph[u]) {
        if (v == p) continue;
        self(self, v, u);
    }
};

dfs(dfs, 0, -1);
```

## Function Objects

Some STL types need a comparator type, not just a one-off lambda.

```cpp
struct ByDistance {
    bool operator()(const pair<int, int>& a, const pair<int, int>& b) const {
        return a.second > b.second; // min-heap behavior in priority_queue
    }
};

priority_queue<pair<int, int>, vector<pair<int, int>>, ByDistance> pq;
```

For `priority_queue`, the comparator feels inverted: it returns true when the left item has lower priority than the right item.

## Common Pitfalls

- Passing a vector by value to a recursive helper.
- Capturing loop variables by reference when the lambda outlives the iteration.
- Writing non-strict comparators with `<=` or `>=`.
- Forgetting the trailing return type for recursive lambdas with multiple return paths.
- Confusing `sort` comparators with `priority_queue` comparators.

## Self-Check

1. What does a `sort` comparator promise?
2. Why is `return a <= b;` invalid as a comparator?
3. When should you capture by reference in a lambda?
4. Why can `priority_queue` comparator logic feel reversed?
