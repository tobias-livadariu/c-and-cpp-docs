# Hash Maps, Sets, And Frequency

## Learning Objectives

You should be able to:

- Use hash maps and sets for frequency, membership, and index lookup.
- Choose ordered containers when order queries matter.
- Build composite keys safely.
- Avoid accidental insertion and duplicate-erasure bugs.

## Recognition Signals

- You need to know whether a value was seen before.
- You need counts of values or characters.
- Brute force repeatedly searches a list.
- You need first index, last index, or grouping by key.
- You need predecessor/successor or sorted iteration.

## Frequency With `unordered_map`

```cpp
unordered_map<int, int> freq;
freq.reserve(2 * n);

for (int x : nums) {
    ++freq[x];
}
```

Check existence without insertion:

```cpp
auto it = freq.find(x);
if (it != freq.end()) {
    cout << it->second << '\n';
}
```

`freq[x]` inserts `x` with value `0` if missing.

## Small Alphabet Frequency

```cpp
array<int, 26> cnt{};
for (char c : s) {
    ++cnt[c - 'a'];
}
```

This is faster and simpler than a hash map when the key space is small and known.

## Two Sum

```cpp
vector<int> two_sum(const vector<int>& nums, int target) {
    unordered_map<int, int> index;

    for (int i = 0; i < static_cast<int>(nums.size()); ++i) {
        int need = target - nums[i];
        auto it = index.find(need);
        if (it != index.end()) {
            return {it->second, i};
        }
        index[nums[i]] = i;
    }
    return {};
}
```

Invariant: `index` contains values from positions before `i`.

## Prefix Sum Counts

Count subarrays with sum `k`:

```cpp
int count_subarrays(const vector<int>& a, int k) {
    unordered_map<long long, int> count;
    count[0] = 1;

    long long pref = 0;
    int ans = 0;

    for (int x : a) {
        pref += x;
        if (auto it = count.find(pref - k); it != count.end()) {
            ans += it->second;
        }
        ++count[pref];
    }
    return ans;
}
```

Works with negative numbers, unlike many sliding window sum templates.

## Grouping

Group anagrams:

```cpp
unordered_map<string, vector<string>> groups;

for (string word : words) {
    string key = word;
    sort(key.begin(), key.end());
    groups[key].push_back(word);
}
```

Alternative key for lowercase words:

```cpp
array<int, 26> cnt{};
```

`array<int, 26>` can be used as a key in `map`, but needs a custom hash for `unordered_map`.

## Sets For Membership

```cpp
unordered_set<int> seen;
for (int x : nums) {
    if (seen.count(x)) return true;
    seen.insert(x);
}
```

`count` returns `0` or `1` for unique-key sets.

## Ordered Set For Neighbor Queries

```cpp
set<int> s;

auto it = s.lower_bound(x);
if (it != s.end()) {
    int successor = *it;
}
if (it != s.begin()) {
    int predecessor = *prev(it);
}
```

Use ordered containers for:

- lower/upper bound by key,
- predecessor/successor,
- sorted output,
- deterministic iteration.

## `multiset`

Use for duplicate values with sorted order.

```cpp
multiset<int> ms;
ms.insert(5);
ms.insert(5);

auto it = ms.find(5);
if (it != ms.end()) {
    ms.erase(it);  // one copy
}
```

`ms.erase(5)` removes all copies of `5`.

## Composite Keys

For `map`, pairs work directly:

```cpp
map<pair<int, int>, int> dist;
dist[{r, c}] = 0;
```

For `unordered_map`, provide a hash:

```cpp
struct PairHash {
    size_t operator()(const pair<int, int>& p) const {
        size_t a = static_cast<unsigned int>(p.first);
        size_t b = static_cast<unsigned int>(p.second);
        return (a << 32) ^ b;
    }
};

unordered_map<pair<int, int>, int, PairHash> mp;
```

For grid problems with bounded columns, encode:

```cpp
int key = r * cols + c;
```

Use `long long` if multiplication may overflow `int`.

## Hash Table Performance

Average `O(1)` does not mean guaranteed `O(1)`.

Good habits:

```cpp
unordered_map<int, int> mp;
mp.reserve(2 * n);
mp.max_load_factor(0.7);
```

For adversarial CP inputs, some people use custom randomized hashes. Use that only when necessary.

## Common Pitfalls

- Using `mp[key]` to check existence and accidentally inserting keys.
- Forgetting that `unordered_map` has no useful iteration order.
- Using unordered containers when you need `lower_bound`.
- Erasing all duplicates from `multiset` when only one should be removed.
- Using a string key built by repeated concatenation in a tight loop when an array or encoded integer would work.

## Self-Check

1. What does `unordered_map::operator[]` do for a missing key?
2. When is `map` better than `unordered_map`?
3. How do you erase one copy from a `multiset`?
4. Why does prefix sum plus hash map handle negative numbers better than sliding window?
