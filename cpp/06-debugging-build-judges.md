# Debugging, Builds, And Online Judges

## Learning Objectives

You should be able to:

- Compile C++ locally with useful warnings.
- Use sanitizers and assertions to catch undefined behavior.
- Prepare judge-friendly input/output.
- Recognize common C++ runtime failure modes.

## Compile Commands

Fast local run:

```bash
g++ -std=c++17 -O2 -Wall -Wextra -pedantic main.cpp -o main
```

Debug run:

```bash
g++ -std=c++17 -g -O0 -Wall -Wextra -Wshadow -fsanitize=address,undefined main.cpp -o main
```

Warnings are part of the feedback loop. Treat them as suspicious until proven harmless.

## Fast I/O

```cpp
ios::sync_with_stdio(false);
cin.tie(nullptr);
```

This disconnects C++ streams from C streams and avoids flushing `cout` before every `cin`.

Do not mix `scanf`/`printf` with `cin`/`cout` after disabling sync.

## Local Test Harness

For CP-style problems:

```bash
./main < input.txt
```

Use tiny tests first:

- minimum input,
- one element,
- duplicate values,
- already sorted,
- reverse sorted,
- all equal,
- maximum value ranges that can overflow.

## Assertions

```cpp
#include <cassert>

assert(0 <= u && u < n);
```

Assertions are for internal assumptions, not user-facing error handling. They are useful while developing templates such as DSU, segment tree, and graph traversals.

## Undefined Behavior

Undefined behavior means the program has no required meaning. It may pass locally and fail on the judge.

Common sources:

- signed integer overflow,
- out-of-bounds array/vector access,
- dereferencing null or dangling pointers,
- using an invalidated iterator,
- missing return from a non-void function,
- shifting by a negative amount or by at least the bit width.

## Address And Undefined Behavior Sanitizers

Compile with:

```bash
g++ -std=c++17 -g -O0 -fsanitize=address,undefined main.cpp -o main
```

This catches many memory and UB bugs quickly. Remove sanitizers for final judge submissions if they are too slow or unsupported.

## Floating Point

Use integer arithmetic when the problem allows it. For comparisons:

```cpp
const double EPS = 1e-9;
if (abs(a - b) < EPS) {
    // approximately equal
}
```

For binary search on real values, use a fixed iteration count:

```cpp
for (int iter = 0; iter < 100; ++iter) {
    double mid = (lo + hi) / 2.0;
    if (can(mid)) hi = mid;
    else lo = mid;
}
```

## Recursion Depth

Deep recursion can stack overflow. This matters for trees and graphs with up to `2e5` nodes.

Options:

- convert DFS to iterative,
- increase stack only if the judge allows it,
- use BFS,
- use recursion for smaller constraints.

Iterative DFS:

```cpp
vector<int> seen(n);
stack<int> st;
st.push(start);
seen[start] = 1;

while (!st.empty()) {
    int u = st.top();
    st.pop();
    for (int v : graph[u]) {
        if (seen[v]) continue;
        seen[v] = 1;
        st.push(v);
    }
}
```

## Submission Hygiene

Before submitting:

- remove debug prints,
- confirm input indexing is correct,
- check integer types,
- test `n = 0` or `n = 1` if allowed,
- test duplicates and ties,
- test disconnected graphs if relevant,
- test maximum path or sum sizes.

## Common Pitfalls

- Debug build passes, optimized build exposes UB.
- Sanitizers change timing and memory use.
- Recursive DFS fails only on a long chain.
- `int` distance overflows in weighted graph problems.
- `cout << endl` causes avoidable slowness.

## Self-Check

1. What does `-fsanitize=address,undefined` help catch?
2. Why can recursive DFS fail on a path-shaped tree?
3. Why should weighted graph distances often be `long long`?
4. What is undefined behavior, and why is it worse than a normal runtime error?
