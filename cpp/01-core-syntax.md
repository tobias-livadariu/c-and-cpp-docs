# Core Syntax

## Learning Objectives

You should be able to:

- Read and write a complete C++17 program.
- Use variables, conditionals, loops, arrays, strings, and vectors.
- Pass values to functions safely.
- Handle basic standard input and output.

## Minimal Program

```cpp
#include <iostream>

int main() {
    std::cout << "hello\n";
    return 0;
}
```

For DSA practice, you will usually use:

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n;
    cout << n << '\n';
}
```

Prefer `'\n'` over `endl` in performance-sensitive code. `endl` also flushes the output buffer.

## Variables And Types

```cpp
int count = 0;              // usually 32-bit
long long total = 0;        // usually 64-bit
double ratio = 0.5;
bool ok = true;
char ch = 'a';
string s = "abc";
```

Common DSA rule: use `long long` for sums, products, counts of pairs, and distances when values may exceed about 2 billion.

```cpp
int a = 1'000'000'000;
long long bad = a * a;          // overflow happens before assignment
long long good = 1LL * a * a;   // promotes before multiplication
```

## Conditionals

```cpp
if (x < 0) {
    sign = -1;
} else if (x == 0) {
    sign = 0;
} else {
    sign = 1;
}
```

`switch` is useful for discrete values:

```cpp
switch (op) {
    case '+': ans = a + b; break;
    case '-': ans = a - b; break;
    default: throw invalid_argument("unknown operator");
}
```

## Loops

```cpp
for (int i = 0; i < n; ++i) {
    // i = 0..n-1
}

while (left < right) {
    ++left;
}

for (int x : nums) {
    cout << x << '\n';
}
```

Use indices when you need positions. Use range-for when you only need values.

```cpp
for (int& x : nums) {
    x *= 2;       // modifies nums
}

for (const int& x : nums) {
    // avoids copying large objects and prevents modification
}
```

## Arrays And Vectors

Prefer `vector` for variable-size arrays.

```cpp
vector<int> a(n);
for (int i = 0; i < n; ++i) {
    cin >> a[i];
}

a.push_back(42);
a.pop_back();
int last = a.back();
int len = static_cast<int>(a.size());
```

`size()` returns an unsigned type (`size_t`). In DSA code, convert once if you need signed arithmetic:

```cpp
int n = static_cast<int>(a.size());
for (int i = n - 1; i >= 0; --i) {
    // safe if n is int and you handled n == 0 when needed
}
```

Fixed-size arrays:

```cpp
array<int, 3> fixed = {1, 2, 3};  // standard fixed-size container
int raw[3] = {1, 2, 3};           // C-style array
```

Use `array` when the size is known at compile time and you want standard container behavior.

## Strings

```cpp
string s = "leetcode";
int n = static_cast<int>(s.size());

s.push_back('!');
s.pop_back();

char first = s[0];
string sub = s.substr(1, 4);  // start, length
```

Character helpers require `<cctype>`:

```cpp
if (isdigit(static_cast<unsigned char>(ch))) {}
if (isalpha(static_cast<unsigned char>(ch))) {}
char lower = static_cast<char>(tolower(static_cast<unsigned char>(ch)));
```

The cast matters for non-ASCII or negative `char` values. In most LeetCode ASCII inputs it will not surface, but the habit is correct.

## Input And Output

Read token by token:

```cpp
int n;
string s;
cin >> n >> s;
```

Read a whole line:

```cpp
string line;
getline(cin, line);
```

If you mixed `cin >> x` with `getline`, consume the leftover newline:

```cpp
int n;
cin >> n;
cin.ignore(numeric_limits<streamsize>::max(), '\n');

string line;
getline(cin, line);
```

## Functions

```cpp
int add(int a, int b) {
    return a + b;
}
```

Pass small values by value:

```cpp
int f(int x);
```

Pass large read-only objects by const reference:

```cpp
int score(const vector<int>& nums);
```

Pass mutable objects by non-const reference:

```cpp
void normalize(vector<int>& nums);
```

Return containers by value when that is the natural API. Modern C++ optimizes common return paths.

```cpp
vector<int> make_prefix(const vector<int>& a) {
    vector<int> pref(a.size() + 1);
    for (int i = 0; i < static_cast<int>(a.size()); ++i) {
        pref[i + 1] = pref[i] + a[i];
    }
    return pref;
}
```

## `auto`

Use `auto` when the type is obvious or verbose:

```cpp
auto it = mp.find(key);
auto [node, dist] = pq.top();  // structured binding, C++17
```

Avoid `auto` when it hides important conversions:

```cpp
auto n = nums.size();  // size_t, not int
```

## Scope And Initialization

Prefer initialization at declaration:

```cpp
int best = numeric_limits<int>::max();
vector<int> dist(n, INF);
```

Use narrower scope:

```cpp
for (int i = 0; i < n; ++i) {
    int x;
    cin >> x;
    // x only exists here
}
```

## Common Pitfalls

- Integer overflow before assignment to `long long`.
- Off-by-one loops with inclusive endpoints.
- Forgetting `cin.tie(nullptr)` and `sync_with_stdio(false)` in large input problems.
- Using `endl` repeatedly.
- Calling `back()`, `front()`, or `pop_back()` on an empty container.
- Iterating backward with an unsigned index.

## Self-Check

1. Why does `long long x = a * b;` still overflow when `a` and `b` are `int`?
2. When should a function take `const vector<int>&` instead of `vector<int>`?
3. Why is `for (int i = n - 1; i >= 0; --i)` dangerous if `n` came from `size()`?
4. What does `endl` do that `'\n'` does not?
