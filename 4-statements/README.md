# 4 Statements

## Expression Statements
Expression followed by a `;`

```cpp
int x{};
++x;
42;
printf("Hello, World!");
```

## Compound Statement
Blocks; A sequence of statements enclosed by `{` `}`.\
Used to combine multiple expressions for
- functions
- if..else
- loops

Each block declares a new block scope
- variables' lifetime is limited to that scope

## Declaration Statement
Used to introduce identifiers like functions, templates and namespaces

### Function Declaration
```cpp
int max(int, int);
```

### Namespace
Prevent naming conflicts

#### Definition

```cpp
namespace AI {
    namespace Pathfinding {
        void AStar(){}
    }
}
```

```cpp
namespace AII::Pathfinding {
    void AStar(){}
}
```

#### Usage

Fully Qualified names:
```cpp
AI::Pathfinding::AStar();
AI::Pathfinding::AStar();
AI::Pathfinding::AStar();
```

Using One Class of a Namespace:
```cpp
using AI::Pathfinding::AStar();
AStar();
AStar();
AStar();
```

Using All Classes of a NameSpace:
```cpp
using namespace AI::Pathfinding;
AStar();
BFS();
Dijkstra();
```

### Type Alias

```cpp
using String = const char[260];
using Vector = CVector3;

String helloWorld{"Hello, World"};
Vector vector{1,2,3};
```

Partial Template Application:
```cpp
// template class
template <typename Key, typename Value>
struct Dictionary{
    void Add(Key key, Value value){}
};

// partial template application
<template typename TContactInfo>
using AddressBook = Dictionary<Name, TContactInfo>

int main(){
    // Dictionary<Name, Employee>:
    AddressBook<Employee> employeeRegister{};
    return 0;
}
```

### Structured Bindings
Allow you to unpack objects:

```cpp
Vector vector{1,2,3};
auto [x, y, z] = vector;
```

### Attributes
Give some additional language-feature options to your declarations

The most important ones:
- `[[noreturn]]`: indicates that a function doesn't return.
- `[[deprecated("reason")]]`: marks a function that shouldn't be used anymore.
- `[[nodiscard]]`: indicates that the returned value of a function should be used.
- `[[maybe_unused]]`: indicates that a value might be unused.

## Selection Statements

### If

```cpp
if(condition){

} else {

}
```

With initialization statement:
```cpp
if(Enemy* enemy = spawnEnemy(); enemy.health > 100){
    // trigger warning sound
}
```

You can use `constexpr` ifs, which are evaluated at compile-time!
- very powerful in combination with
  - template meta-programming
  - `constexpr` functions
  - template functions
- can improve runtime performance again

The following function uses a `constexpr` if.
```cpp
auto value_of(T t){
    if constexpr (std::is_pointer_v<T>::value){
        if(!t) throw std::runtime_error("Null reference exception!");
        return *t;
    } else {
        return t;
    }
}
```

If you use `value_of<int>`, it compiles to:

```cpp
auto value_of(int t){
    return t;
}
```

If you use `value_of<int*>`, it compiles to:

```cpp
auto value_of(T t){
    if(!t) throw std::runtime_error("Null reference exception!");
    return *t;
}
```

### Range-Based For Loops

```cpp
const int numbers[]{1,2,3,4,5,6};
for(const auto number : numbers){

}
```

Defining the Iterator (C#: IEnumerator)
```cpp
struct FibonacciIterator{
    bool operator !=(int x) const {
        return x >= current;
    }

    FibonacciIterator& operator++(){
        const auto tmp = current;
        current += prev;
        prev = tmp;
        return *this;
    }

    int operator*() const {
        return current;
    }
private:
    int current{1}, prev{1};
}
```

Defining the Range (C#: IEnumerable)
```cpp
struct FibonacciRange{
    explicit FibonacciRange(int max) : max{max} {}
    FibonacciIterator begin() const{
        return FibonacciIterator{};
    }
    int end() const{
        return max;
    }
private:
    const int max;
}
```

Using The Range (C#: foreach)
```cpp
for(const auto number : FibonacciRange{100}){

}
```

Using The Range (C#: for + GetEnumerator())
```cpp
FibonacciRange fib{100};
const auto end = fib.end();
for(auto it = fib.begin(); it != end ++it){
    const auto& number = *it;
}
```