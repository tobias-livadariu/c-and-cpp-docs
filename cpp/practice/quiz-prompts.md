# Quiz Prompts For Future Agents

## Purpose

Use this file to ask a future agent to quiz you from this repo. The best quizzes force recall, require small code reconstruction, and ask you to explain invariants.

## Default Quiz Protocol

Ask the agent:

```text
Quiz me from [file path].
Use C++17.
Do not show answers until I respond.
Ask:
1. one recall question,
2. one invariant question,
3. one debugging question,
4. one small coding prompt.
After I answer, grade briefly and give one follow-up.
```

## Difficulty Scale

| Level | What the agent should do |
| --- | --- |
| 1 | Ask direct definitions and fill-in-the-template prompts |
| 2 | Ask one-template coding drills with obvious pattern signals |
| 3 | Mix two patterns, such as binary search plus greedy feasibility |
| 4 | Hide the pattern and require constraint analysis |
| 5 | Ask CP-style variants with proof and edge-case pressure |

## Core C++ Prompts

Use after reading [../01-core-syntax.md](../01-core-syntax.md):

```text
Ask me to write a complete C++17 program that reads n and an array, then prints the maximum subarray sum.
Require fast I/O and long long where appropriate.
```

Use after reading [../02-types-memory-lifetimes.md](../02-types-memory-lifetimes.md):

```text
Give me five snippets involving references, vector mutation, and returned values.
Ask which snippets dangle, copy, move, or invalidate iterators.
```

Use after reading [../03-functions-templates-lambdas.md](../03-functions-templates-lambdas.md):

```text
Ask me to write three comparators:
1. sort pairs by second descending, then first ascending,
2. min-heap of pair<distance,node>,
3. set ordering for a Point struct.
```

Use after reading [../04-classes-oop-raii.md](../04-classes-oop-raii.md):

```text
Ask me to implement a DSU class from memory.
Then ask what invariant the class protects.
```

## STL Prompts

Container choice:

```text
Give me ten mini-scenarios and ask which standard container I would use.
For each answer, require the complexity and one pitfall.
```

Algorithms:

```text
Ask me to explain lower_bound, upper_bound, binary_search, unique, nth_element, and accumulate.
Then give a one-line bug involving each.
```

Strings:

```text
Ask me to parse mixed token and line input, then ask why getline may read an empty line.
```

Utilities:

```text
Ask me to identify overflow bugs involving accumulate, multiplication, INF sentinels, and bit shifts.
```

## DSA Prompts

Arrays and windows:

```text
Give me a problem statement where sliding window works and another where it fails because of negative numbers.
Ask me to explain the monotonicity difference.
```

Hash maps:

```text
Ask me to derive subarray sum equals k using prefix sums and unordered_map.
Require the invariant for the map.
```

Stacks and heaps:

```text
Ask me to implement next greater element and sliding window maximum.
Then ask why one uses a stack and the other uses a deque.
```

Binary search:

```text
Ask me for first-true and last-true templates.
Then give a binary-search-on-answer problem and make me define can(x).
```

Trees and graphs:

```text
Ask me to implement BFS shortest path and directed cycle detection.
Then ask why the same BFS is invalid for weighted graphs.
```

Dijkstra and DSU:

```text
Ask me to implement Dijkstra with a priority_queue and stale-entry skip.
Then ask me to implement Kruskal with DSU.
```

DP:

```text
Give me three DP scenarios.
Make me state dp meaning, transition, base cases, and iteration order before code.
```

Advanced:

```text
Ask me to implement Fenwick tree from memory using 0-based public indices.
Then ask me to find and fix one off-by-one bug.
```

## Review Session Template

Use this after a problem-solving session:

```text
I solved these problems: [list].
Quiz me on the patterns I used.
For each problem, ask:
1. What was the invariant?
2. What standard library tool was essential?
3. What edge case was most dangerous?
4. How would the solution change if constraints doubled?
```

## Error Log Template

Maintain a running error log:

```text
Problem:
Pattern:
Bug:
Root cause:
Correct invariant:
STL/tool lesson:
Retest case:
```

Example:

```text
Problem: Subarray Sum Equals K
Pattern: prefix sum + hashmap
Bug: used sliding window with negative numbers
Root cause: assumed sum shrinks monotonically when left moves
Correct invariant: count[p] is number of previous prefixes with sum p
STL/tool lesson: unordered_map<long long, int> for prefix counts
Retest case: [1, -1, 1], k = 1
```
