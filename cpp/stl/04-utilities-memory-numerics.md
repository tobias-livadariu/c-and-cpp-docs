# Utilities, Memory, And Numerics

## Learning Objectives

You should be able to:

- Use `pair`, `tuple`, structured bindings, and comparison helpers.
- Use `optional`, `variant`, and `function` when appropriate.
- Use smart pointers and RAII at a practical level.
- Apply common numeric utilities in DSA.
- Avoid integer and floating-point traps.

## `pair`

```cpp
pair<int, int> p = {3, 4};
cout << p.first << ' ' << p.second << '\n';
```

Pairs compare lexicographically:

```cpp
vector<pair<int, int>> intervals = {{2, 5}, {1, 3}};
sort(intervals.begin(), intervals.end());  // by first, then second
```

Structured binding:

```cpp
auto [l, r] = intervals[0];
```

Common uses:

- `{distance, node}` in Dijkstra,
- `{start, end}` intervals,
- `{value, index}` sorting,
- coordinate pairs.

## `tuple`

```cpp
tuple<int, int, int> state = {dist, row, col};
auto [d, r, c] = state;
```

Tuples also compare lexicographically.

Use a small `struct` instead when names would make code clearer.

## `tie`

```cpp
int a, b, c;
tie(a, b, c) = state;
```

Useful for unpacking before C++17 or assigning into existing variables.

## `optional`

`optional<T>` represents a value that may be absent.

```cpp
optional<int> find_first_even(const vector<int>& a) {
    for (int x : a) {
        if (x % 2 == 0) return x;
    }
    return nullopt;
}
```

For LeetCode, sentinel values are often dictated by the problem. In application code, `optional` is clearer than magic values.

## `variant` And `any`

`variant` is a type-safe tagged union.

```cpp
variant<int, string> value = 42;
value = string("hello");
```

`any` can hold a value of any copyable type, but you lose compile-time specificity.

These are rarely needed for DSA. Know what they are for broader C++ fluency.

## `function`

`std::function` stores a callable with a specific signature.

```cpp
function<void(int, int)> dfs = [&](int u, int p) {
    for (int v : graph[u]) {
        if (v != p) dfs(v, u);
    }
};
```

It is convenient for recursive lambdas but may add overhead. For most LeetCode recursion, that overhead is fine. For tight CP loops, use a named function or self-parameter lambda.

## Smart Pointers

Unique ownership:

```cpp
auto p = make_unique<int>(42);
```

Shared ownership:

```cpp
auto p = make_shared<vector<int>>(10);
```

Prefer `unique_ptr` unless ownership is genuinely shared. Avoid raw owning pointers.

In LeetCode linked list/tree problems, raw pointers are usually provided and owned by the platform.

## Bit Utilities

C++20 `<bit>`:

```cpp
int c = popcount(static_cast<unsigned int>(mask));
bool one = has_single_bit(static_cast<unsigned int>(mask));
```

Portable compiler builtins are common in CP:

```cpp
int c = __builtin_popcount(mask);
int cll = __builtin_popcountll(mask64);
```

Bit tricks:

```cpp
int lowbit = x & -x;
bool is_set = (mask & (1 << bit)) != 0;
mask |= (1 << bit);
mask &= ~(1 << bit);
mask ^= (1 << bit);
```

Use `1LL << bit` when `bit` may be 31 or larger.

## Numeric Utilities

```cpp
long long sum = accumulate(v.begin(), v.end(), 0LL);
iota(ids.begin(), ids.end(), 0);

int g = gcd(a, b);
int l = lcm(a, b);
```

`numeric_limits`:

```cpp
int inf = numeric_limits<int>::max();
long long linf = numeric_limits<long long>::max() / 4;
```

Use divided max values for distances so addition does not overflow.

```cpp
const long long INF = numeric_limits<long long>::max() / 4;
if (dist[u] + w < dist[v]) {
    dist[v] = dist[u] + w;
}
```

## Randomness

```cpp
mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());
uniform_int_distribution<int> dist(1, 6);
int roll = dist(rng);
```

Use `<random>` instead of `rand()` for serious randomness. In CP, randomness is rare but can appear in hashing or randomized algorithms.

## Floating Point

```cpp
double x = sqrt(2.0);
```

Comparison:

```cpp
const double EPS = 1e-9;
if (abs(a - b) <= EPS) {}
```

Prefer integer comparisons when possible:

```cpp
// Compare a / b < c / d without floating point, if values fit.
return 1LL * a * d < 1LL * c * b;
```

## Common Pitfalls

- `accumulate` with `0` instead of `0LL`.
- Shifting `1 << bit` when `bit >= 31`.
- Using `abs(INT_MIN)`, which overflows for `int`.
- Storing Dijkstra distances in `int` when edge sums can exceed range.
- Using `rand()` and modulo for high-quality randomness.
- Overusing `tuple` where a named struct would be clearer.

## Self-Check

1. How do pairs sort by default?
2. Why is `numeric_limits<long long>::max() / 4` often safer than the full max?
3. When is `optional<T>` clearer than returning `-1`?
4. Why should bit shifts often use `1LL`?
