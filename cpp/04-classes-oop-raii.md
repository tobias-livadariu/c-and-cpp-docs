# Classes, OOP, And RAII

## Learning Objectives

You should be able to:

- Define structs and classes.
- Use constructors, member functions, and access control.
- Understand RAII and the Rule of Zero.
- Use inheritance and virtual functions when they are actually needed.
- Build small DSA helper types without overengineering.

## `struct` Versus `class`

The only default difference is visibility.

```cpp
struct Point {
    int x = 0;
    int y = 0;
};

class Counter {
    int value = 0;  // private by default

public:
    void inc() { ++value; }
    int get() const { return value; }
};
```

In DSA code, use `struct` for simple data carriers.

```cpp
struct Edge {
    int to;
    int weight;
};
```

## Constructors

```cpp
struct Point {
    int x;
    int y;

    Point() : x(0), y(0) {}
    Point(int x, int y) : x(x), y(y) {}
};
```

Prefer member initializer lists, especially for `const` members, references, and expensive members.

```cpp
class Graph {
    int n;
    vector<vector<int>> adj;

public:
    explicit Graph(int n) : n(n), adj(n) {}

    void add_edge(int u, int v) {
        adj[u].push_back(v);
        adj[v].push_back(u);
    }
};
```

Use `explicit` for single-argument constructors unless implicit conversion is intentional.

## Const Member Functions

```cpp
class Counter {
    int value = 0;

public:
    int get() const {
        return value;
    }
};
```

The `const` after `get()` means the function will not modify the observable state of the object.

## RAII And The Rule Of Zero

Prefer types that manage their resources automatically.

```cpp
class Buffer {
    vector<int> data;

public:
    explicit Buffer(int n) : data(n) {}
};
```

Because `vector` handles memory, `Buffer` does not need a destructor, copy constructor, move constructor, or assignment operators. This is the Rule of Zero.

The Rule of Five matters when your class directly owns a raw resource:

- destructor,
- copy constructor,
- copy assignment,
- move constructor,
- move assignment.

Most code should avoid manually owning raw resources.

## Operator Overloading

Useful for sorting or storing custom objects.

```cpp
struct Point {
    int x;
    int y;

    bool operator<(const Point& other) const {
        if (x != other.x) return x < other.x;
        return y < other.y;
    }
};
```

This enables:

```cpp
set<Point> points;
sort(v.begin(), v.end());
```

For equality and hash-based containers:

```cpp
struct PairHash {
    size_t operator()(const pair<int, int>& p) const {
        return (static_cast<size_t>(p.first) << 32) ^ static_cast<unsigned int>(p.second);
    }
};

unordered_set<pair<int, int>, PairHash> seen;
```

## Inheritance

Inheritance models an "is-a" relationship.

```cpp
class Shape {
public:
    virtual ~Shape() = default;
    virtual double area() const = 0;
};

class Circle : public Shape {
    double r;

public:
    explicit Circle(double r) : r(r) {}
    double area() const override {
        return 3.141592653589793 * r * r;
    }
};
```

Key rules:

- Use a virtual destructor in a polymorphic base class.
- Mark overrides with `override`.
- Prefer composition when you just need a helper object.
- For LeetCode and CP, inheritance is rare outside platform-provided classes.

## Encapsulation

Encapsulation protects invariants.

```cpp
class DSU {
    vector<int> parent;
    vector<int> size;

public:
    explicit DSU(int n) : parent(n), size(n, 1) {
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }

    bool unite(int a, int b) {
        a = find(a);
        b = find(b);
        if (a == b) return false;
        if (size[a] < size[b]) swap(a, b);
        parent[b] = a;
        size[a] += size[b];
        return true;
    }
};
```

The class makes it hard to accidentally mutate `parent` without preserving DSU invariants.

## Common Pitfalls

- Forgetting `public:` in a `class`.
- Forgetting `const` on read-only member functions.
- Using inheritance when a member field would be simpler.
- Owning raw memory and then accidentally copying it.
- Missing a virtual destructor in a polymorphic base class.

## Self-Check

1. What is the default visibility difference between `struct` and `class`?
2. What problem does RAII solve?
3. Why should a polymorphic base class have a virtual destructor?
4. Why is `explicit` useful on a single-argument constructor?
