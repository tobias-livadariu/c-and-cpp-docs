# Iterators, Ranges, And Algorithms

## Learning Objectives

You should be able to:

- Use iterator pairs and half-open ranges.
- Apply core `<algorithm>` and `<numeric>` tools.
- Use binary search helpers correctly.
- Use heap and permutation algorithms when adaptors are not enough.
- Know where C++20 ranges fit.

## Iterator Pair Model

Most STL algorithms operate on `[first, last)`.

```cpp
sort(v.begin(), v.end());
reverse(v.begin() + l, v.begin() + r);  // indices l..r-1
```

For a whole container:

```cpp
auto first = begin(v);
auto last = end(v);
```

Iterator categories matter:

| Category | Example | Supports |
| --- | --- | --- |
| input | stream input | one-pass reading |
| forward | `forward_list` | multi-pass forward |
| bidirectional | `list`, `set`, `map` | forward and backward |
| random access | `vector`, `deque`, `array`, `string` | indexing-style jumps |
| contiguous | `vector`, `array`, `string` | contiguous memory |

`sort` needs random-access iterators, so it works on `vector` but not `list`.

## Finding And Counting

```cpp
auto it = find(v.begin(), v.end(), target);
if (it != v.end()) {
    int idx = static_cast<int>(it - v.begin());
}

int c = count(v.begin(), v.end(), target);

int odds = count_if(v.begin(), v.end(), [](int x) {
    return x % 2 != 0;
});
```

Use container member `find` for associative containers:

```cpp
if (mp.find(key) != mp.end()) {}
```

## Min And Max

```cpp
int mn = *min_element(v.begin(), v.end());
int mx = *max_element(v.begin(), v.end());
auto [lo, hi] = minmax_element(v.begin(), v.end());
```

Never dereference the result on an empty range.

## Sorting

```cpp
sort(v.begin(), v.end());
sort(v.rbegin(), v.rend());
stable_sort(v.begin(), v.end());
```

Partial sorting:

```cpp
partial_sort(v.begin(), v.begin() + k, v.end());  // smallest k sorted
nth_element(v.begin(), v.begin() + k, v.end());   // v[k] placed as if sorted
```

`nth_element` is useful for order statistics when you do not need full sorting.

## Binary Search Helpers

On a sorted range:

```cpp
bool exists = binary_search(v.begin(), v.end(), x);

auto lo = lower_bound(v.begin(), v.end(), x); // first >= x
auto hi = upper_bound(v.begin(), v.end(), x); // first > x

int less_than_x = static_cast<int>(lo - v.begin());
int count_x = static_cast<int>(hi - lo);
```

For ordered containers:

```cpp
auto it = s.lower_bound(x);  // member function, O(log n)
```

Do not use free `lower_bound(s.begin(), s.end(), x)` on `set`; it may do linear iterator movement.

## Unique And Deduplication

`unique` removes adjacent duplicates logically and returns the new end.

```cpp
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

Without sorting first, only consecutive duplicates are removed.

## Partitioning

```cpp
auto mid = partition(v.begin(), v.end(), [](int x) {
    return x % 2 == 0;
});
```

After partition, elements satisfying the predicate are before `mid`; order is not necessarily preserved.

Use `stable_partition` if relative order matters.

## Transform And Fill

```cpp
fill(v.begin(), v.end(), 0);

transform(v.begin(), v.end(), v.begin(), [](int x) {
    return x * x;
});
```

Copy with inserters:

```cpp
vector<int> odds;
copy_if(v.begin(), v.end(), back_inserter(odds), [](int x) {
    return x % 2;
});
```

## Numeric Algorithms

```cpp
long long sum = accumulate(v.begin(), v.end(), 0LL);

vector<int> ids(n);
iota(ids.begin(), ids.end(), 0);

int g = gcd(a, b);
int l = lcm(a, b);
```

Prefix sums:

```cpp
vector<long long> pref(n + 1);
partial_sum(v.begin(), v.end(), pref.begin() + 1);
```

Or manually, which is often clearer:

```cpp
for (int i = 0; i < n; ++i) {
    pref[i + 1] = pref[i] + v[i];
}
```

## Heap Algorithms

`priority_queue` is usually simpler, but heap algorithms are useful when you already have a vector.

```cpp
vector<int> h = {3, 1, 4};
make_heap(h.begin(), h.end());       // max-heap
h.push_back(10);
push_heap(h.begin(), h.end());

pop_heap(h.begin(), h.end());        // max moved to back
int mx = h.back();
h.pop_back();
```

Min-heap:

```cpp
make_heap(h.begin(), h.end(), greater<int>());
```

## Permutations

```cpp
sort(v.begin(), v.end());
do {
    // use permutation
} while (next_permutation(v.begin(), v.end()));
```

Useful for small `n`, brute force ordering, and generating lexicographic permutations.

## Ranges In C++20

C++20 ranges let many algorithms accept a range directly:

```cpp
// C++20
ranges::sort(v);
auto it = ranges::lower_bound(v, x);
```

Views can describe lazy transformations:

```cpp
// C++20
for (int x : v | views::filter([](int y) { return y % 2; })) {
    cout << x << '\n';
}
```

For LeetCode and CP, classic iterator algorithms are still the safest baseline because compiler and library support varies.

## Common Pitfalls

- Calling binary search helpers on unsorted ranges.
- Using free `lower_bound` on `set` or `map` instead of member functions.
- Forgetting `unique` does not erase by itself.
- Dereferencing `min_element` on an empty range.
- Using `accumulate(..., 0)` when the sum needs `long long`.

## Self-Check

1. What does `lower_bound` return?
2. Why is `accumulate(v.begin(), v.end(), 0LL)` safer than using `0`?
3. Why does `unique` usually appear with `erase`?
4. When would `nth_element` be better than `sort`?
