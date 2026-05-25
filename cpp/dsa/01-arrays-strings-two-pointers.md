# Arrays, Strings, Two Pointers, And Sliding Windows

## Learning Objectives

You should be able to:

- Use two pointers for sorted arrays, partitioning, and palindrome-style scans.
- Use fixed and variable sliding windows.
- Use prefix sums and difference arrays.
- Avoid substring and erase-in-loop performance traps.

## Recognition Signals

- The problem asks about a contiguous subarray or substring.
- The input is sorted or can be sorted.
- You need a pair, triplet, or interval satisfying a condition.
- You can maintain an answer while moving each pointer only forward.

## Opposite-End Two Pointers

Use when the array is sorted and moving left/right changes the value monotonically.

```cpp
bool has_pair_sum(vector<int> a, int target) {
    sort(a.begin(), a.end());
    int l = 0;
    int r = static_cast<int>(a.size()) - 1;

    while (l < r) {
        int sum = a[l] + a[r];
        if (sum == target) return true;
        if (sum < target) ++l;
        else --r;
    }
    return false;
}
```

Invariant: all pairs outside `[l, r]` have already been proven impossible.

## Same-Direction Two Pointers

Use when both pointers only increase.

```cpp
int remove_duplicates(vector<int>& a) {
    if (a.empty()) return 0;

    int write = 1;
    for (int read = 1; read < static_cast<int>(a.size()); ++read) {
        if (a[read] != a[read - 1]) {
            a[write++] = a[read];
        }
    }
    return write;
}
```

Invariant: `a[0..write)` contains the processed unique values.

## Fixed Sliding Window

Use when every candidate has exactly size `k`.

```cpp
long long max_sum_k(const vector<int>& a, int k) {
    long long window = 0;
    long long best = LLONG_MIN;

    for (int r = 0; r < static_cast<int>(a.size()); ++r) {
        window += a[r];

        if (r >= k) {
            window -= a[r - k];
        }

        if (r >= k - 1) {
            best = max(best, window);
        }
    }
    return best;
}
```

Invariant: after removal, `window` is the sum of the last at most `k` elements ending at `r`.

## Variable Sliding Window

Use when a window expands until invalid, then shrinks until valid.

Example: longest subarray with sum at most `target`, assuming nonnegative numbers.

```cpp
int longest_sum_at_most(const vector<int>& a, long long target) {
    int best = 0;
    int left = 0;
    long long sum = 0;

    for (int right = 0; right < static_cast<int>(a.size()); ++right) {
        sum += a[right];

        while (sum > target) {
            sum -= a[left];
            ++left;
        }

        best = max(best, right - left + 1);
    }
    return best;
}
```

This relies on nonnegative values. If negatives exist, shrinking no longer has monotonic behavior; consider prefix sums, hash maps, or balanced trees.

## Longest Substring Without Repeating Characters

```cpp
int length_of_longest_substring(const string& s) {
    array<int, 256> last;
    last.fill(-1);

    int best = 0;
    int left = 0;

    for (int right = 0; right < static_cast<int>(s.size()); ++right) {
        unsigned char c = static_cast<unsigned char>(s[right]);
        if (last[c] >= left) {
            left = last[c] + 1;
        }
        last[c] = right;
        best = max(best, right - left + 1);
    }
    return best;
}
```

Invariant: `s[left..right]` has no duplicate characters.

## Prefix Sums

Use when many range sum queries appear.

```cpp
vector<long long> pref(n + 1);
for (int i = 0; i < n; ++i) {
    pref[i + 1] = pref[i] + a[i];
}

long long sum_lr = pref[r] - pref[l];  // [l, r)
```

Subarray sum equals `k`:

```cpp
int subarray_sum_equals_k(const vector<int>& a, int k) {
    unordered_map<long long, int> seen;
    seen[0] = 1;

    long long pref = 0;
    int ans = 0;

    for (int x : a) {
        pref += x;
        if (seen.count(pref - k)) ans += seen[pref - k];
        ++seen[pref];
    }
    return ans;
}
```

Invariant: `seen[p]` is how many previous prefixes had sum `p`.

## Difference Arrays

Use for range update, final point query.

```cpp
vector<long long> diff(n + 1);

auto add_range = [&](int l, int r, int x) {
    // add x to [l, r)
    diff[l] += x;
    diff[r] -= x;
};

for (int i = 1; i < n; ++i) {
    diff[i] += diff[i - 1];
}
```

## String Performance Habits

Avoid building many substrings:

```cpp
// Often too expensive in nested loops.
string t = s.substr(l, len);
```

Prefer indices:

```cpp
bool equal_range(const string& s, int a, int b, int len) {
    for (int i = 0; i < len; ++i) {
        if (s[a + i] != s[b + i]) return false;
    }
    return true;
}
```

## Common Pitfalls

- Using sliding window when numbers can be negative and monotonicity is gone.
- Forgetting to update frequency when shrinking.
- Sorting input when original indices are needed, without preserving indices.
- Treating `[l, r]` and `[l, r)` inconsistently.
- Repeated `erase` from the front of a `vector` or `string`, causing `O(n^2)`.

## Self-Check

1. Why does variable sliding window usually require monotonicity?
2. What does `pref[r] - pref[l]` represent?
3. Why do two pointers work for sorted pair sum?
4. What invariant does the write pointer keep in in-place compaction?
