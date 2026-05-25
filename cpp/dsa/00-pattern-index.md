# DSA Pattern Index

## Objective

Use this directory while solving problems. Each file answers:

- When should I recognize this pattern?
- What invariant should I write down?
- What C++ STL tools should I reach for?
- What template can I rebuild from memory?
- What edge cases should I test?

## Pattern Map

| Pattern | File | Common C++ tools |
| --- | --- | --- |
| Arrays, strings, two pointers, sliding window | [01-arrays-strings-two-pointers.md](01-arrays-strings-two-pointers.md) | `vector`, `string`, `array`, `deque` |
| Hash maps, sets, frequency | [02-hashmaps-sets-frequency.md](02-hashmaps-sets-frequency.md) | `unordered_map`, `unordered_set`, `map`, `set` |
| Stacks, queues, heaps | [03-stacks-queues-heaps.md](03-stacks-queues-heaps.md) | `stack`, `queue`, `deque`, `priority_queue` |
| Binary search and greedy | [04-binary-search-greedy.md](04-binary-search-greedy.md) | `sort`, `lower_bound`, `priority_queue` |
| Trees and graphs | [05-trees-graphs.md](05-trees-graphs.md) | adjacency lists, DFS, BFS |
| Shortest paths, MST, union-find | [06-shortest-paths-mst-union-find.md](06-shortest-paths-mst-union-find.md) | `priority_queue`, DSU |
| Dynamic programming | [07-dynamic-programming.md](07-dynamic-programming.md) | `vector`, memo tables |
| Advanced CP patterns | [08-advanced-patterns.md](08-advanced-patterns.md) | Fenwick, segment tree, trie, sparse table |

## Problem Solving Checklist

Before coding:

1. Restate the input, output, and constraints.
2. Find the brute force.
3. Identify the repeated expensive operation.
4. Choose a data structure or invariant to remove that expense.
5. Write edge cases.

During coding:

1. Name index meaning: `left`, `right`, `mid`, `u`, `v`, `mask`.
2. Keep ranges half-open when possible.
3. Use `long long` for sums, counts of pairs, and weighted distances.
4. Prefer STL algorithms for sorting, binary search, and min/max.

After coding:

1. Test minimum input.
2. Test all equal values.
3. Test duplicates and ties.
4. Test sorted and reverse-sorted inputs.
5. Test disconnected graphs or impossible states.
6. Test overflow-sized values.

## Recognition Signals

| Signal in problem | Likely pattern |
| --- | --- |
| sorted input, pair sum, palindrome | two pointers |
| contiguous subarray or substring | sliding window or prefix sum |
| count occurrences, first seen index | hash map |
| next greater/smaller, nested intervals | monotonic stack |
| shortest unweighted path | BFS |
| weighted nonnegative shortest path | Dijkstra |
| "minimum possible maximum" | binary search on answer |
| choose intervals/tasks optimally | greedy, often sort first |
| overlapping subproblems | dynamic programming |
| connectivity under unions | DSU |
| range sum updates/queries | Fenwick or segment tree |
| static idempotent range minimum | sparse table |

## Invariant Template

Write one sentence before code:

```text
At the start of each loop, ...
```

Examples:

- Sliding window: `counts` describes exactly `s[left..right]`.
- Binary search: the answer is always in `[lo, hi]`.
- Dijkstra: finalized nodes have their shortest distance fixed.
- DP: `dp[i]` is the answer for the prefix ending before index `i`.

## Self-Check

1. What is the brute force and what makes it too slow?
2. Which operation needs to become `O(1)`, `O(log n)`, or amortized cheap?
3. What invariant can you state before writing code?
4. What input breaks the most naive implementation?
