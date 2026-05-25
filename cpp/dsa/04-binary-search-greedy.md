# Binary Search And Greedy

## Learning Objectives

You should be able to:

- Use binary search over arrays and over answer space.
- Write first-true and last-true templates.
- Recognize greedy problems and prove the local choice.
- Combine sorting with greedy decisions.

## Binary Search Recognition Signals

- Input is sorted.
- You need `O(log n)` search.
- The answer is the minimum feasible value or maximum feasible value.
- A predicate changes from false to true once.

## First True Template

Find the smallest `x` in `[lo, hi]` such that `can(x)` is true.

```cpp
while (lo < hi) {
    int mid = lo + (hi - lo) / 2;
    if (can(mid)) {
        hi = mid;
    } else {
        lo = mid + 1;
    }
}
```

Invariant: the first true answer is always in `[lo, hi]`.

## Last True Template

Find the largest `x` in `[lo, hi]` such that `can(x)` is true.

```cpp
while (lo < hi) {
    int mid = lo + (hi - lo + 1) / 2;
    if (can(mid)) {
        lo = mid;
    } else {
        hi = mid - 1;
    }
}
```

The `+1` prevents infinite loops when `lo + 1 == hi`.

## STL Binary Search

```cpp
auto it = lower_bound(v.begin(), v.end(), x); // first >= x
auto jt = upper_bound(v.begin(), v.end(), x); // first > x

int idx = static_cast<int>(it - v.begin());
int count_x = static_cast<int>(jt - it);
```

Requires sorted range.

For `set` and `map`, use member functions:

```cpp
auto it = s.lower_bound(x);
```

## Binary Search On Answer

Example: minimum ship capacity to ship packages within `days`.

```cpp
long long ship_within_days(const vector<int>& w, int days) {
    long long lo = *max_element(w.begin(), w.end());
    long long hi = accumulate(w.begin(), w.end(), 0LL);

    auto can = [&](long long cap) {
        int used = 1;
        long long cur = 0;
        for (int x : w) {
            if (cur + x > cap) {
                ++used;
                cur = 0;
            }
            cur += x;
        }
        return used <= days;
    };

    while (lo < hi) {
        long long mid = lo + (hi - lo) / 2;
        if (can(mid)) hi = mid;
        else lo = mid + 1;
    }
    return lo;
}
```

Key question: if `can(x)` is true, is `can(x + 1)` also true? If yes, first-true binary search may apply.

## Binary Search On Real Values

```cpp
for (int iter = 0; iter < 100; ++iter) {
    double mid = (lo + hi) / 2.0;
    if (can(mid)) hi = mid;
    else lo = mid;
}
```

Fixed iterations avoid precision loop traps.

## Greedy Recognition Signals

- You can sort by a key and make one pass.
- You need maximum number of non-overlapping intervals.
- You repeatedly choose earliest finish, smallest cost, largest benefit, or currently best candidate.
- A local choice can be justified by an exchange argument.

## Interval Scheduling

Choose the maximum number of non-overlapping intervals.

```cpp
int max_non_overlapping(vector<pair<int, int>> intervals) {
    sort(intervals.begin(), intervals.end(), [](auto a, auto b) {
        if (a.second != b.second) return a.second < b.second;
        return a.first < b.first;
    });

    int ans = 0;
    int last_end = INT_MIN;

    for (auto [l, r] : intervals) {
        if (l >= last_end) {
            ++ans;
            last_end = r;
        }
    }
    return ans;
}
```

Greedy choice: taking the interval that ends earliest leaves the most room for future intervals.

## Merge Intervals

```cpp
vector<pair<int, int>> merge_intervals(vector<pair<int, int>> intervals) {
    sort(intervals.begin(), intervals.end());

    vector<pair<int, int>> merged;
    for (auto [l, r] : intervals) {
        if (merged.empty() || merged.back().second < l) {
            merged.push_back({l, r});
        } else {
            merged.back().second = max(merged.back().second, r);
        }
    }
    return merged;
}
```

## Greedy With Heap

Meeting rooms:

```cpp
int min_rooms(vector<pair<int, int>> intervals) {
    sort(intervals.begin(), intervals.end());
    priority_queue<int, vector<int>, greater<int>> ends;

    int best = 0;
    for (auto [start, end] : intervals) {
        while (!ends.empty() && ends.top() <= start) {
            ends.pop();
        }
        ends.push(end);
        best = max(best, static_cast<int>(ends.size()));
    }
    return best;
}
```

Heap invariant: active meetings are represented by their end times.

## Greedy Proof Sketch

Use one of these:

- Exchange argument: show any optimal solution can be transformed to include your greedy choice without getting worse.
- Staying ahead: show after each step your solution is at least as good as any alternative by the metric that matters.
- Cut property: for MST-style problems, the cheapest edge crossing a cut is safe.

## Common Pitfalls

- Binary searching a predicate that is not monotonic.
- Infinite loop in last-true search because `mid` rounds down.
- Using `int hi = accumulate(..., 0)` when sum can overflow.
- Sorting by the wrong interval endpoint.
- Assuming greedy works because it feels natural but lacking an exchange argument.

## Self-Check

1. What must be true about `can(x)` for binary search on answer?
2. Why does last-true binary search use upper mid?
3. What does `lower_bound` return?
4. What is an exchange argument?
