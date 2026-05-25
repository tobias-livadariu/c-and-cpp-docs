# Strings, Text, And I/O

## Learning Objectives

You should be able to:

- Use `string` safely and efficiently.
- Parse token-based and line-based input.
- Use character helpers correctly.
- Know when `stringstream`, `charconv`, `regex`, and formatting tools are appropriate.

## `string`

```cpp
string s = "abc";
s.push_back('d');
s += "ef";

cout << s.size() << '\n';
cout << s[0] << '\n';
```

Substring:

```cpp
string t = s.substr(pos, len);
```

`substr` copies. In many DSA problems that is fine; in tight loops, prefer indices.

Lexicographic comparison:

```cpp
if (a < b) {
    // dictionary-like order by character values
}
```

## Building Strings

Repeated `+=` is usually okay amortized, but reserve when size is known.

```cpp
string out;
out.reserve(n);
for (char c : s) {
    if (c != '#') out.push_back(c);
}
```

## Character Helpers

Include `<cctype>`.

```cpp
bool digit = isdigit(static_cast<unsigned char>(c));
bool letter = isalpha(static_cast<unsigned char>(c));
char lower = static_cast<char>(tolower(static_cast<unsigned char>(c)));
```

In simple ASCII-only judge problems, you often see:

```cpp
if (isdigit(c)) {}
```

The cast version is the robust habit.

## Token Input

```cpp
int n;
string word;
cin >> n >> word;
```

`operator>>` skips whitespace and reads one token.

## Line Input

```cpp
string line;
getline(cin, line);
```

After token input:

```cpp
int n;
cin >> n;
cin.ignore(numeric_limits<streamsize>::max(), '\n');

string line;
getline(cin, line);
```

## `stringstream`

Useful for parsing a line with whitespace-separated fields.

```cpp
string line = "10 20 30";
stringstream ss(line);

int x;
vector<int> nums;
while (ss >> x) {
    nums.push_back(x);
}
```

It is convenient but not the fastest option for massive input.

## Numeric Conversion

Simple:

```cpp
int x = stoi("123");
long long y = stoll("1234567890123");
string s = to_string(42);
```

Faster, lower-level C++17:

```cpp
int x = 0;
string s = "123";
auto [ptr, ec] = from_chars(s.data(), s.data() + s.size(), x);
if (ec == errc()) {
    // parsed
}
```

`from_chars` does not allocate and does not throw, but its ergonomics are lower.

## `string_view`

`string_view` is a non-owning view into characters.

```cpp
string s = "abcdef";
string_view view(s.data() + 1, 3);  // "bcd"
```

It does not own data. Do not store it beyond the lifetime of the referenced string.

For most DSA problems, indices are simpler and less error-prone.

## Formatting Output

Floating precision:

```cpp
cout << fixed << setprecision(10) << ans << '\n';
```

Column width:

```cpp
cout << setw(5) << x << '\n';
```

C++20 `format` and C++23 `print` are nice in modern application code but support varies on judges.

## Regex

```cpp
regex re("[a-z]+");
bool ok = regex_match(s, re);
```

Regex is heavy and often slower than manual parsing for DSA. Use it only when the problem is truly text-pattern oriented and constraints allow it.

## File I/O

```cpp
ifstream in("input.txt");
ofstream out("output.txt");

int n;
in >> n;
out << n << '\n';
```

Most online judges use standard input and output. Some CP platforms historically require named files.

## Common String Patterns

Frequency:

```cpp
array<int, 26> freq{};
for (char c : s) {
    ++freq[c - 'a'];
}
```

Two pointers:

```cpp
bool palindrome = true;
for (int l = 0, r = static_cast<int>(s.size()) - 1; l < r; ++l, --r) {
    if (s[l] != s[r]) {
        palindrome = false;
        break;
    }
}
```

Rolling construction:

```cpp
string cleaned;
for (char c : s) {
    if (isalnum(static_cast<unsigned char>(c))) {
        cleaned.push_back(static_cast<char>(tolower(static_cast<unsigned char>(c))));
    }
}
```

## Common Pitfalls

- Creating substrings inside nested loops.
- Mixing `cin >>` and `getline` without consuming the newline.
- Treating bytes as full Unicode characters.
- Using regex when a loop would be linear and clearer.
- Calling `s[i]` without checking bounds.

## Self-Check

1. Why can `substr` be too expensive in a loop?
2. What is the difference between `cin >> s` and `getline(cin, s)`?
3. Why can `string_view` dangle?
4. When should you use `setprecision`?
