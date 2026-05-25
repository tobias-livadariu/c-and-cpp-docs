# Types, Memory, And Lifetimes

## Learning Objectives

You should be able to:

- Distinguish values, references, pointers, and iterators.
- Explain stack lifetime, heap lifetime, and RAII.
- Avoid dangling references and invalid iterators.
- Use `const`, copying, moving, and smart pointers at a practical level.

## Mental Model

C++ makes ownership and lifetime visible. Most bugs come from using an object after its lifetime ended, keeping a reference after the owner changed, or assuming a cheap copy is cheap.

## Values

A value owns its own object.

```cpp
int a = 5;
int b = a;  // copy
b = 10;     // a is still 5
```

Containers are values too:

```cpp
vector<int> x = {1, 2, 3};
vector<int> y = x;  // deep copy of elements
```

Copying a large vector inside a loop is a common accidental `O(n^2)` bug.

## References

A reference is another name for an existing object. It cannot be reseated.

```cpp
int x = 5;
int& ref = x;
ref = 7;  // x is now 7
```

Use const references for read-only parameters:

```cpp
long long sum(const vector<int>& nums) {
    long long ans = 0;
    for (int x : nums) ans += x;
    return ans;
}
```

Do not return a reference to a local variable:

```cpp
// Wrong: local dies when the function returns.
const string& bad() {
    string s = "dead";
    return s;
}
```

## Pointers

A pointer stores an address. It can be null and can be reseated.

```cpp
int x = 5;
int* p = &x;
*p = 9;

if (p != nullptr) {
    cout << *p << '\n';
}
```

In LeetCode tree/list problems, raw pointers are common because the platform owns the nodes:

```cpp
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
};
```

In your own application code, prefer standard containers or smart pointers for ownership.

## Stack And Heap

Automatic storage, often called stack allocation:

```cpp
void f() {
    vector<int> nums(10);  // vector object has automatic lifetime
}                          // nums is destroyed here
```

Dynamic storage, often called heap allocation:

```cpp
auto p = make_unique<int>(42);
```

The pointer-like object `p` owns the allocation and releases it automatically. This is RAII: acquire a resource in an object, release it in that object's destructor.

## RAII

RAII means resource ownership is tied to object lifetime.

```cpp
{
    vector<int> nums(1'000'000);
} // memory released automatically
```

This applies to memory, files, locks, sockets, and other resources. Most modern C++ code should not need naked `new` and `delete`.

## `const`

`const` means the object cannot be modified through that name.

```cpp
const int limit = 10;

void print(const vector<int>& nums) {
    // nums.push_back(1);  // error
}
```

Pointer constness:

```cpp
const int* p1;  // pointer to const int
int* const p2 = &x;  // const pointer to int
const int* const p3 = &x;  // const pointer to const int
```

For DSA, the most important habit is using `const T&` for read-only large parameters.

## Copying And Moving

Copy duplicates an object:

```cpp
vector<int> b = a;
```

Move transfers resources when the old object is no longer needed:

```cpp
vector<int> make() {
    vector<int> v = {1, 2, 3};
    return v;  // efficient return
}

vector<int> a = {1, 2, 3};
vector<int> b = std::move(a);  // a is valid but unspecified
```

In DSA solutions, you rarely need to write `std::move`. Understand it mainly to avoid surprise when moved-from objects are still valid but should not be relied on for old contents.

## Iterator And Reference Invalidation

Many containers invalidate references, pointers, or iterators after mutation.

`vector`:

- `push_back` may reallocate, invalidating all iterators and references.
- `erase` invalidates iterators at and after the erased position.
- `reserve` can prevent repeated reallocations.

```cpp
vector<int> v;
v.reserve(n);
```

`deque` has different invalidation rules and is useful when pushing or popping at both ends.

`set`, `map`, `unordered_set`, and `unordered_map` keep references to existing elements more stable than `vector`, but erasing an element invalidates references/iterators to that element. Unordered containers may invalidate iterators on rehash.

## `string_view` And `span`

`string_view` (C++17) and `span` (C++20) are non-owning views. They do not extend lifetime.

```cpp
string_view view = "abc";
```

Danger:

```cpp
string_view bad;
{
    string s = "temporary";
    bad = s;
}
// bad dangles
```

For LeetCode, `string_view` is rarely necessary. Use `string` and indices until you have a reason.

## Common Pitfalls

- Returning references to locals.
- Storing references to `vector` elements, then calling `push_back`.
- Copying large containers by value in recursive functions.
- Assuming moved-from objects keep their old contents.
- Using raw owning pointers instead of containers or smart pointers.

## Self-Check

1. What is the difference between `vector<int> v` and `vector<int>& v` as a parameter?
2. Why can `push_back` invalidate a pointer to `v[0]`?
3. What does RAII buy you compared with manual `new` and `delete`?
4. What does it mean that a moved-from object is valid but unspecified?
