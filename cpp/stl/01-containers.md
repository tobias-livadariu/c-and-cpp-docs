# Containers

## Learning Objectives

You should be able to:

- Choose a standard container based on access, insertion, deletion, ordering, and lookup needs.
- Use sequence, ordered associative, unordered associative, and adaptor containers.
- Explain basic complexity and invalidation tradeoffs.
- Avoid common container misuse in DSA problems.

## Container Decision Table

| Need | Default choice | Why |
| --- | --- | --- |
| Dynamic array, random access | `vector<T>` | contiguous, cache-friendly, fast indexing |
| Fixed-size array | `array<T, N>` | no heap allocation, standard container API |
| Push/pop both ends | `deque<T>` | efficient front and back operations |
| Stack | `stack<T>` or `vector<T>` | LIFO |
| Queue | `queue<T>` or `deque<T>` | FIFO |
| Min/max by priority | `priority_queue<T>` | heap adaptor |
| Sorted unique keys | `set<T>` | order and `lower_bound` |
| Sorted key-value pairs | `map<K,V>` | order and `lower_bound` |
| Fast membership average case | `unordered_set<T>` | hash table |
| Fast key-value lookup average case | `unordered_map<K,V>` | hash table |
| Duplicates with sorted order | `multiset<T>` | ordered duplicate keys |
| Fixed-size boolean flags | `bitset<N>` | compact bit operations |

## `vector`

`vector<T>` is the default DSA container.

```cpp
vector<int> v;
v.reserve(n);

v.push_back(10);
v.push_back(20);

cout << v[0] << '\n';
cout << v.back() << '\n';
```

Key operations:

| Operation | Complexity |
| --- | --- |
| `v[i]` | `O(1)` |
| `push_back` | amortized `O(1)` |
| `pop_back` | `O(1)` |
| insert/erase in middle | `O(n)` |
| `sort(v.begin(), v.end())` | `O(n log n)` |

Use `reserve` when you know the final size but will push elements gradually.

```cpp
vector<int> v;
v.reserve(n);
for (int i = 0; i < n; ++i) {
    int x;
    cin >> x;
    v.push_back(x);
}
```

2D vector:

```cpp
vector<vector<int>> grid(rows, vector<int>(cols, 0));
```

Pitfall:

```cpp
vector<int> v;
v[0] = 5;  // wrong: size is still 0
```

Use `resize` if you need indexed assignment:

```cpp
vector<int> v(n);
v[0] = 5;
```

## `array`

`array<T, N>` is fixed-size and stack-friendly for small `N`.

```cpp
array<int, 4> dir = {-1, 0, 1, 0};
```

It supports `.size()`, range-for, and STL algorithms.

## `deque`

`deque<T>` supports efficient insertion/removal at both ends.

```cpp
deque<int> dq;
dq.push_back(1);
dq.push_front(0);
dq.pop_back();
dq.pop_front();
```

Use it for:

- BFS queues,
- monotonic queues,
- sliding window maximum/minimum.

## `stack`

`stack<T>` is an adaptor over another container.

```cpp
stack<int> st;
st.push(1);
st.push(2);
int x = st.top();
st.pop();
```

Useful for:

- parentheses,
- monotonic stack,
- iterative DFS,
- next greater element.

You cannot iterate a `stack`. Use `vector` if you need stack behavior plus iteration.

## `queue`

```cpp
queue<int> q;
q.push(start);

while (!q.empty()) {
    int u = q.front();
    q.pop();
}
```

Useful for BFS and level-order traversal.

## `priority_queue`

Default is max-heap:

```cpp
priority_queue<int> pq;
pq.push(5);
pq.push(2);
cout << pq.top();  // 5
```

Min-heap:

```cpp
priority_queue<int, vector<int>, greater<int>> pq;
```

Pairs compare lexicographically:

```cpp
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
pq.push({dist, node});
```

For Dijkstra, store `{distance, node}` and skip stale entries.

```cpp
auto [d, u] = pq.top();
pq.pop();
if (d != dist[u]) continue;
```

## Ordered Sets And Maps

`set<T>` stores unique sorted keys.

```cpp
set<int> s;
s.insert(5);
s.insert(2);

auto it = s.lower_bound(4);  // first element >= 4
if (it != s.end()) cout << *it << '\n';
```

`map<K,V>` stores sorted key-value pairs.

```cpp
map<string, int> freq;
++freq["apple"];

for (const auto& [key, value] : freq) {
    cout << key << ' ' << value << '\n';
}
```

Operations are usually `O(log n)`.

Use ordered containers when:

- you need sorted iteration,
- you need predecessor/successor,
- you need `lower_bound` by key,
- worst-case guarantees matter more than average-case speed.

## Multisets

`multiset<T>` stores sorted duplicate keys.

```cpp
multiset<int> ms;
ms.insert(5);
ms.insert(5);

auto it = ms.find(5);
if (it != ms.end()) ms.erase(it);  // erases one copy
```

Pitfall:

```cpp
ms.erase(5);  // erases all copies equal to 5
```

## Unordered Sets And Maps

`unordered_set<T>` and `unordered_map<K,V>` are hash tables.

```cpp
unordered_map<int, int> freq;
for (int x : nums) {
    ++freq[x];
}
```

Average operations are `O(1)`, worst case `O(n)`.

Use them for:

- frequency counting,
- first/last index lookup,
- membership tests,
- graph compression maps.

Reserve when many keys are expected:

```cpp
unordered_map<int, int> mp;
mp.reserve(2 * n);
mp.max_load_factor(0.7);
```

Custom hash for pairs:

```cpp
struct PairHash {
    size_t operator()(const pair<int, int>& p) const {
        size_t a = static_cast<unsigned int>(p.first);
        size_t b = static_cast<unsigned int>(p.second);
        return (a << 32) ^ b;
    }
};

unordered_set<pair<int, int>, PairHash> seen;
```

## `bitset`

`bitset<N>` is fixed at compile time.

```cpp
bitset<1000> seen;
seen.set(5);
seen.reset(5);
seen.flip(7);
cout << seen.count() << '\n';
```

Useful for:

- compact visited flags,
- subset DP when `N` is known,
- bit-parallel tricks.

For dynamic size, use `vector<bool>` cautiously or `vector<unsigned long long>` manually when performance matters.

## Iterator Invalidation Summary

| Container | Common invalidation concern |
| --- | --- |
| `vector` | reallocation invalidates all iterators/references |
| `deque` | insert/erase can invalidate broadly |
| `list` | erased elements invalidate only themselves |
| `set`/`map` | erased elements invalidate only themselves |
| `unordered_*` | rehash invalidates iterators |

Safe habit: after mutating a container, do not keep using old iterators unless you know the rule.

## Common Pitfalls

- Using `map[key]` only to check existence. It inserts a default value.
- Erasing all duplicates from a `multiset` accidentally.
- Keeping a `vector` reference across `push_back`.
- Expecting `unordered_map` iteration order to be stable.
- Using `list` for speed. It is usually slower due to poor cache locality.

## Self-Check

1. Why is `vector` the default sequence container?
2. When do you need `set` instead of `unordered_set`?
3. How do you make a min-heap with `priority_queue`?
4. What is the difference between `ms.erase(value)` and `ms.erase(iterator)`?
