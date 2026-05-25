# Standard Library Header Map

## Learning Objectives

You should be able to:

- Distinguish the STL from the broader C++ standard library.
- Know which headers matter most for DSA and CP.
- Choose explicit includes when portability matters.
- Recognize C++20/23/26 headers without depending on them blindly.

## STL Versus Standard Library

Strictly, the STL refers to the classic model of containers, iterators, algorithms, and function objects. In everyday C++ conversation, people often say "STL" when they mean the C++ standard library.

This guide covers the standard library with emphasis on STL-style tools useful for DSA.

## Include Strategy

Portable:

```cpp
#include <algorithm>
#include <iostream>
#include <queue>
#include <unordered_map>
#include <vector>
```

Contest shortcut on GCC/Clang libstdc++:

```cpp
#include <bits/stdc++.h>
using namespace std;
```

`<bits/stdc++.h>` is not standard. It is convenient when accepted by the judge.

## DSA Priority Headers

Know these cold:

| Header | Use |
| --- | --- |
| `<algorithm>` | `sort`, `lower_bound`, `max_element`, `next_permutation`, heap algorithms |
| `<array>` | fixed-size standard array |
| `<bitset>` | compact fixed-size bit sets |
| `<cassert>` | local assumptions |
| `<cmath>` | math functions |
| `<cstdint>` | fixed-width integers such as `int64_t` |
| `<deque>` | double-ended queue, monotonic queue |
| `<functional>` | `greater`, function objects, hashing helpers |
| `<iostream>` | `cin`, `cout` |
| `<limits>` | numeric limits |
| `<map>` | ordered key-value map |
| `<numeric>` | `accumulate`, `iota`, `gcd`, `lcm`, prefix-like operations |
| `<queue>` | `queue`, `priority_queue` |
| `<set>` | ordered set and multiset |
| `<stack>` | stack adaptor |
| `<string>` | `string` |
| `<tuple>` | tuple and structured binding support |
| `<unordered_map>` | hash map |
| `<unordered_set>` | hash set |
| `<utility>` | `pair`, `swap`, `move` |
| `<vector>` | dynamic arrays |

## Containers

| Header | Main tools | Notes |
| --- | --- | --- |
| `<array>` | `array<T, N>` | fixed-size, contiguous |
| `<vector>` | `vector<T>` | default sequence container |
| `<deque>` | `deque<T>` | fast push/pop at both ends |
| `<list>` | `list<T>` | doubly linked list, rarely best for CP |
| `<forward_list>` | `forward_list<T>` | singly linked list, rare in DSA solutions |
| `<set>` | `set`, `multiset` | ordered keys, `O(log n)` |
| `<map>` | `map`, `multimap` | ordered key-value, `O(log n)` |
| `<unordered_set>` | `unordered_set`, `unordered_multiset` | average `O(1)` hashed keys |
| `<unordered_map>` | `unordered_map`, `unordered_multimap` | average `O(1)` hashed key-value |
| `<stack>` | `stack<T>` | adaptor, LIFO |
| `<queue>` | `queue<T>`, `priority_queue<T>` | FIFO and heap adaptor |
| `<span>` | `span<T>` | C++20 non-owning contiguous view |
| `<flat_set>` | `flat_set`, `flat_multiset` | C++23 sorted-vector-like adaptor |
| `<flat_map>` | `flat_map`, `flat_multimap` | C++23 sorted-vector-like map adaptor |
| `<mdspan>` | `mdspan` | C++23 multidimensional non-owning view |
| `<inplace_vector>` | `inplace_vector` | C++26 fixed-capacity resizable vector |
| `<hive>` | `hive` | C++26 sequence container with stable references after erasure reuse |

For LeetCode and CP, focus on `vector`, `deque`, `set`, `map`, unordered containers, `stack`, `queue`, and `priority_queue`.

## Algorithms And Iterators

| Header | Main tools | Notes |
| --- | --- | --- |
| `<algorithm>` | sorting, searching, heap, permutation, min/max, partition | core DSA header |
| `<numeric>` | `accumulate`, `iota`, `partial_sum`, `gcd`, `lcm` | numeric range algorithms |
| `<iterator>` | iterator tags, `begin`, `end`, inserters | useful for generic code |
| `<ranges>` | C++20 ranges and views | useful in modern code, less reliable on judges |
| `<execution>` | parallel execution policies | rarely useful on online judges |

## Strings And Text

| Header | Main tools | Notes |
| --- | --- | --- |
| `<string>` | `string`, `basic_string` | core string type |
| `<string_view>` | `string_view` | C++17 non-owning string view |
| `<cstring>` | C string functions | `strlen`, `memset`, `memcpy` |
| `<cctype>` | character classification | `isdigit`, `tolower` |
| `<charconv>` | `to_chars`, `from_chars` | fast numeric conversion, C++17 |
| `<sstream>` | `stringstream` | parsing simple text |
| `<regex>` | regular expressions | often slow/heavy for CP |
| `<format>` | `format` | C++20 formatting, judge support varies |
| `<print>` | `print`, `println` | C++23, support varies |

## Utilities

| Header | Main tools | Notes |
| --- | --- | --- |
| `<utility>` | `pair`, `move`, `forward`, `swap` | everywhere |
| `<tuple>` | `tuple`, `tie`, structured binding support | useful for multi-key values |
| `<functional>` | `less`, `greater`, `function`, `hash` | comparators and callbacks |
| `<optional>` | `optional<T>` | C++17 maybe-present value |
| `<variant>` | `variant` | C++17 tagged union |
| `<any>` | `any` | C++17 type-erased value, rare in DSA |
| `<bit>` | bit operations | C++20, `popcount`, rotations |
| `<compare>` | three-way comparison | C++20 |
| `<type_traits>` | compile-time type traits | template-heavy code |
| `<concepts>` | concepts | C++20 generic constraints |

## Memory

| Header | Main tools | Notes |
| --- | --- | --- |
| `<memory>` | `unique_ptr`, `shared_ptr`, `make_unique` | ownership |
| `<new>` | low-level allocation support | rare |
| `<memory_resource>` | polymorphic memory resources | C++17, advanced |
| `<scoped_allocator>` | nested allocator support | advanced |

For DSA, standard containers usually handle memory for you.

## Numerics

| Header | Main tools | Notes |
| --- | --- | --- |
| `<cmath>` | `sqrt`, `abs`, `floor`, `ceil` | math |
| `<cstdlib>` | `abs`, `rand`, conversions | prefer `<random>` for randomness |
| `<random>` | random engines and distributions | use `mt19937` |
| `<complex>` | complex numbers | geometry/signal problems |
| `<numbers>` | math constants | C++20 |
| `<valarray>` | numeric arrays | rare |
| `<cfenv>` | floating-point environment | rare |
| `<ratio>` | compile-time rational arithmetic | rare |

## I/O And Files

| Header | Main tools | Notes |
| --- | --- | --- |
| `<iostream>` | `cin`, `cout`, `cerr` | standard streams |
| `<iomanip>` | `setprecision`, `setw` | formatted output |
| `<sstream>` | `stringstream` | string parsing |
| `<fstream>` | file streams | local file I/O |
| `<ios>` | stream base types | usually included indirectly |
| `<istream>` | input streams | usually included indirectly |
| `<ostream>` | output streams | usually included indirectly |
| `<streambuf>` | stream buffers | advanced |
| `<filesystem>` | paths, directory traversal | C++17, not DSA-heavy |

## Diagnostics And Language Support

| Header | Main tools | Notes |
| --- | --- | --- |
| `<cassert>` | `assert` | local debug checks |
| `<stdexcept>` | standard exceptions | application code |
| `<exception>` | exception base utilities | application code |
| `<system_error>` | error codes | systems code |
| `<limits>` | `numeric_limits<T>` | min/max values |
| `<climits>` | C integer limits | legacy constants |
| `<cstddef>` | `size_t`, `nullptr_t` | basic types |
| `<cstdint>` | `int64_t`, `uint32_t` | fixed-width integers |
| `<initializer_list>` | initializer lists | usually included indirectly |
| `<source_location>` | source location info | C++20 diagnostics |
| `<version>` | feature-test macros | C++20 |

## Time And Concurrency

| Header | Main tools | Notes |
| --- | --- | --- |
| `<chrono>` | clocks and durations | benchmarking, time logic |
| `<ctime>` | C time utilities | legacy |
| `<thread>` | threads | not used by most judges |
| `<mutex>` | locks | concurrency |
| `<atomic>` | atomics | concurrency |
| `<future>` | futures/promises | concurrency |
| `<condition_variable>` | blocking coordination | concurrency |
| `<semaphore>` | semaphores | C++20 |
| `<latch>` | latches | C++20 |
| `<barrier>` | barriers | C++20 |

Online judges usually run single-threaded solutions. Do not reach for concurrency in DSA unless the problem explicitly asks for it.

## C Compatibility Headers

Useful C-style headers in C++ form:

| Header | Common uses |
| --- | --- |
| `<cstdio>` | `printf`, `scanf` |
| `<cstdlib>` | `abs`, `exit`, conversions |
| `<cstring>` | `memset`, `memcpy`, `strlen` |
| `<cctype>` | `isdigit`, `tolower` |
| `<cmath>` | math functions |
| `<cstdint>` | fixed-width integers |
| `<cerrno>` | C error reporting |

Prefer the `c...` versions (`<cstring>`) over `.h` versions (`<string.h>`) in C++.

## Self-Check

1. Which headers do you need for `priority_queue`, `pair`, `sort`, and `accumulate`?
2. Why is `<bits/stdc++.h>` convenient but non-portable?
3. Which standard library categories are most important for LeetCode?
4. When would you choose `<map>` over `<unordered_map>`?
