# Paradigms And Idioms

## Learning Objectives

You should be able to:

- Recognize procedural, object-oriented, generic, and functional C++ styles.
- Use the STL's iterator and algorithm model.
- Write code around invariants and half-open ranges.
- Choose simple styles for DSA and stronger abstractions when complexity demands it.

## The Practical C++ Blend

C++ is multi-paradigm. Good C++ often combines:

- Procedural code for direct algorithms.
- Generic programming through templates and STL containers.
- Object-oriented design for invariant-heavy types.
- Functional patterns through lambdas and standard algorithms.
- RAII for resource management.

For LeetCode and CP, the best solution is often procedural plus STL. Use classes when they protect a real invariant, such as DSU, Fenwick tree, trie, or segment tree.

## Procedural Style

Procedural style is direct and stateful.

```cpp
int max_subarray_sum(const vector<int>& a) {
    int best = a[0];
    int cur = a[0];
    for (int i = 1; i < static_cast<int>(a.size()); ++i) {
        cur = max(a[i], cur + a[i]);
        best = max(best, cur);
    }
    return best;
}
```

Use it when the algorithm is naturally a sequence of steps.

## Generic Programming

The STL is built around generic components:

- containers store elements,
- iterators describe positions,
- algorithms operate over iterator ranges,
- comparators and predicates customize behavior.

```cpp
template <class It>
long long sum_range(It first, It last) {
    return accumulate(first, last, 0LL);
}
```

This works for `vector<int>`, `array<int, N>`, and many other iterator ranges.

## Functional Patterns

Functional style emphasizes transformation over manual mutation.

```cpp
vector<int> a = {3, 1, 4, 1, 5};

int odds = count_if(a.begin(), a.end(), [](int x) {
    return x % 2 == 1;
});

transform(a.begin(), a.end(), a.begin(), [](int x) {
    return x * x;
});
```

This can be clearer when the operation is a simple map, filter, count, or reduction.

Do not force functional style when a loop exposes the invariant more clearly.

## Object-Oriented Style

Use OOP when data and operations share invariants.

```cpp
class MedianTracker {
    priority_queue<int> lo;
    priority_queue<int, vector<int>, greater<int>> hi;

public:
    void add(int x) {
        if (lo.empty() || x <= lo.top()) lo.push(x);
        else hi.push(x);

        if (lo.size() > hi.size() + 1) {
            hi.push(lo.top());
            lo.pop();
        } else if (hi.size() > lo.size()) {
            lo.push(hi.top());
            hi.pop();
        }
    }

    double median() const {
        if (lo.size() == hi.size()) return (lo.top() + hi.top()) / 2.0;
        return lo.top();
    }
};
```

The class protects the balancing invariant between heaps.

## Half-Open Ranges

C++ standard algorithms usually use `[first, last)`: include `first`, exclude `last`.

```cpp
sort(a.begin(), a.end());
reverse(a.begin() + l, a.begin() + r);  // reverses indices l..r-1
```

Benefits:

- Empty range is `first == last`.
- Length is `last - first` for random-access iterators.
- Adjacent ranges compose: `[l, m)` and `[m, r)`.

Use this in your own DSA reasoning:

```cpp
// window is [left, right)
while (right < n) {
    add(a[right]);
    ++right;

    while (!valid()) {
        remove(a[left]);
        ++left;
    }
}
```

## Invariants

An invariant is a fact your code keeps true.

Binary search example:

```cpp
// answer is in [lo, hi]
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (can(mid)) hi = mid;
    else lo = mid + 1;
}
```

Sliding window example:

```cpp
// counts represent exactly s[left..right]
```

Heap example:

```cpp
// pq contains candidates not yet finalized
```

Writing the invariant before code is one of the fastest ways to reduce off-by-one errors.

## Error Handling Styles

For contest code:

- Return sentinel values when the problem statement defines them.
- Use `assert` for internal assumptions during local debugging.
- Avoid exceptions unless the platform or project expects them.

For application code:

- Use exceptions for unrecoverable API failures when appropriate.
- Use `optional<T>` for maybe-present values.
- Use `expected<T, E>` in C++23 codebases that support it.

## Common Idioms

Fast I/O:

```cpp
ios::sync_with_stdio(false);
cin.tie(nullptr);
```

Min/max update:

```cpp
best = min(best, candidate);
```

Coordinate directions:

```cpp
const int dr[4] = {-1, 0, 1, 0};
const int dc[4] = {0, 1, 0, -1};
```

Infinity:

```cpp
const int INF = 1e9;
const long long LINF = 4e18;
```

Erase-remove:

```cpp
v.erase(remove(v.begin(), v.end(), x), v.end());
```

Sort and unique:

```cpp
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

## Common Pitfalls

- Using abstractions that obscure the invariant.
- Treating `[l, r]` and `[l, r)` interchangeably.
- Using `unordered_map` when deterministic order or `lower_bound` is needed.
- Using a loop when a standard algorithm would be clearer, or using an algorithm when a loop would be clearer.

## Self-Check

1. What does `[first, last)` mean?
2. Why do STL algorithms use iterators instead of container-specific member functions?
3. When is a class better than a free function in DSA code?
4. Write the invariant for binary search on the first true value.
