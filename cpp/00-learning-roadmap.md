# C++ Learning Roadmap

## Objective

Rebuild enough C++ fluency to solve DSA problems cleanly, reason about standard library choices, and move toward competitive programming.

By the end of this guide, you should be able to:

- Write correct C++17 solutions from memory.
- Pick the right container for a workload.
- Use iterators, algorithms, lambdas, and comparators without fighting the language.
- Recognize common DSA patterns and translate them into C++ templates.
- Explain your choices to a future tutor or agent.

## Baseline

Use C++17 unless a judge or project explicitly supports newer standards.

```bash
g++ -std=c++17 -O2 -Wall -Wextra -pedantic main.cpp -o main
```

For local debugging, prefer:

```bash
g++ -std=c++17 -g -O0 -Wall -Wextra -Wshadow -fsanitize=address,undefined main.cpp -o main
```

Common online judge setup:

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    return 0;
}
```

`<bits/stdc++.h>` is a GCC convenience header, not standard C++. Use it for CP if accepted. Use explicit includes for interviews, libraries, and portable code.

## Recommended Order

1. Core syntax: [01-core-syntax.md](01-core-syntax.md)
2. Types, memory, and lifetime: [02-types-memory-lifetimes.md](02-types-memory-lifetimes.md)
3. Functions, templates, and lambdas: [03-functions-templates-lambdas.md](03-functions-templates-lambdas.md)
4. Classes, OOP, and RAII: [04-classes-oop-raii.md](04-classes-oop-raii.md)
5. Paradigms and idioms: [05-paradigms-and-idioms.md](05-paradigms-and-idioms.md)
6. Debugging and build habits: [06-debugging-build-judges.md](06-debugging-build-judges.md)
7. STL and standard library notes in [stl](stl)
8. DSA patterns in [dsa](dsa)

## How These Notes Are Structured

Most chapters follow this pattern:

- Learning objectives: what you should be able to do.
- Mental model: the rule that prevents memorization from becoming brittle.
- Syntax and examples: small, reusable C++17 snippets.
- Pitfalls: common ways to lose correctness or time.
- Self-check: active recall prompts.

This structure is deliberate. Learning objectives make the expected skill observable, and self-check questions force recall instead of passive rereading.

## Weekly Review Loop

Use this cycle while solving LeetCode or CP sets:

1. Pick one pattern, such as binary search on answer.
2. Read the corresponding note.
3. Implement the template from memory.
4. Solve 3 problems:
   - one easy template match,
   - one medium with a twist,
   - one problem you previously missed.
5. Write a short postmortem:
   - What was the invariant?
   - What container did I use and why?
   - What edge case broke first?
   - What would I quiz myself on tomorrow?

## Agent Teaching Protocol

When asking a future agent to quiz you, give it:

- The file you are studying.
- Your comfort level from 1 to 5.
- Whether you want recall questions, debugging drills, or full problems.

Example:

```text
Quiz me from cpp/dsa/04-binary-search-greedy.md.
I am a 2/5 on binary search on answer.
Ask one concept question, one invariant question, and one coding prompt.
Do not show the solution until I answer.
```

## Progress Checklist

- [ ] I can write basic input/output and loops without looking.
- [ ] I can explain value, reference, pointer, and move semantics at a practical level.
- [ ] I can write custom comparators for `sort`, `set`, and `priority_queue`.
- [ ] I can choose between `vector`, `deque`, `set`, `map`, and unordered containers.
- [ ] I can use `lower_bound`, `upper_bound`, `sort`, `accumulate`, and heap algorithms.
- [ ] I can implement BFS, DFS, Dijkstra, union-find, and common DP shapes.
- [ ] I can debug off-by-one errors using invariants.
