# 5 Functions v2

## Declaration

```cpp
prefixModifiers returnType functionName(arguments) suffixModifiers;
```

### Prefix Modifiers
- `static`
- `virtual`
- `constexpr`
- `[[noreturn]]`
- `inline`

### Function Call
1. Arguments are placed into registers and on the call stack.
2. Return address is pushed onto the call stack.
3. Jump to the called function
4. When function returns, jump to the return address
5. Free the call stack.

This overhead can be avoided by inlining functions.
- `inline` keyword helps the compiler make this decision
- but in the end it's up to the compiler to decide

### Suffix Modifers
- `noexcept`
- `const`
- `final`
- `override`
- `volatile`

## Auto Return Type
```cpp
auto getFive(){ return 5; }
```

```cpp
template<typename X, typename Y>
auto add(X x, Y y) -> decltype(x+y){
    return x+y;
}
```

## Overload Resolution
Describes the process in which the compiler chooses, which function to invoke in case multiple options are possible

1. exact type match
2. integral and floating-point promotions (`short` to `int` or `float` to `double`)
3. standard conversions like integral to floating-point or child to parent type
4. user-defined conversions
5. variadic function

## Variadic Functions
Take a variable number of arguments

Requires use of these functions to access the arguments:
- `va_list`: declare a local variable representing the arguments
- `va_start`: get access to the arguments
- `va_end`: end iteration
- `va_arg`: get current argument
- `va_copy`: copy the arguments

```cpp
int sum(int count, ...){
    va_list args;
    va_start(args, count);
    int result{};
    while(n--){
        auto next = va_arg(args, int);
        result += next;
    }
    va_end(args);
    return result;
}
```

Problem:
- Not Type-Safe (`va_arg(args, int)`)
- Number of Elements must be tracked manually (`count`)

### Solution: Variadic Templates
As mentioned before

```cpp
template<typename T>
constexpr T sum(T x){
    return x;
}

template<typename T, typename... Args>
constexpr T sum(T x, Args... args){
    // compile-time recursion
    return x + sum(args...);
}
```

### Fold Expression
If you want apply a simple binary operator over all arguments of a parameter pack:

```cpp
template <typename... T>
constexpr auto sum(T... args){
    return (... + args);
}
```

## Function Pointers

### Declaration

```cpp
// return type  variable name   argument list  null
         float  (*operation)   (float, float)   {};
```

Using Type Alias:

```cpp
using MathFunction = float(*)(float, int);
//...
MathFunction operation;
```

### Assignment

```cpp
float add(float a, float b){
    return a+b;
}
float subtract(float a, float b){
    return a-b;
}

// ...
operation = add;
operation = subtract;
```

### Invocation
```cpp
float result = operation(3,5);
```

## Lambda Expressions
There are five components:
- `captures`: member variables of the function object
  - these are variables that the function accesses outside its body
- `parameters`: arguments required to invoke the function
- `body`: the function code
- `modifiers`: like `constexpr` and `noexcept`
- `return type`: return type of the function object

### Function Template Argument

```cpp
template <typename Function>
void transform(int* array, size_t length, Function function){
    for(size_t i{}; i < length; ++i){
        array[i] = function(array[i]);
    }
}
```

### Providing Lambda Functions

```cpp
const size_t count = 3;
int numbers[count]{1,2,3};
transform(numbers, count, [](int x){return x*2;}); // 2, 4, 6
transform(numbers, count, [](int x){return x-1;}); // 1, 3, 5
```

### Generic Lambdas

```cpp
auto translate = [](auto x){return 10*x+5;};
```

### Lambda Return Type
Automatically Deducted, you can also manually define it:

```cpp
[](int x, double d) -> double {return x+y;}
```

Using `decltype`:

```cpp
[](int x, double d) -> decltype(x+y) {return x+y;}
```

### Lambda Captures
#### By Value:
```cpp
Player* player{};
auto onFinish = [player](){
    // we can use the copy of the captured variable
    player.spawn();
}
loadGame(onFinish);
```

#### ByReference:
```cpp
SaveGame* saveGame{};
auto onServerResponse = new [&saveGame](SaveGame* result){
    // we can assign new values to the referenced variable
    saveGame = result;
}
loginOnServer(onServerResponse);
```

#### Default Capture (Value):
```cpp
Player* player{};
auto onFinish = [=](){
    // player is automatically captured by value
    player.spawn();
}
loadGame(onFinish);
```

#### Per Default (Reference):
```cpp
SaveGame* saveGame{};
auto onServerResponse = new [&](SaveGame* result){
    // saveGame is automatically captured by reference
    saveGame = result;
}
loginOnServer(onServerResponse);
```

#### Initializer Expression
Allows you to rename captured variables
- this sometimes makes their intended use clearer

```cpp
Player* player{};
auto onFinish = [objectToSpawn=player](){
    objectToSpawn.spawn();
}
loadGame(onFinish);
```

#### Capturing This By Value
```cpp
startGame([*this](){
    // access this object by value
});
```

#### Capturing This By Reference
```cpp
startGame([this](){
    // access this object by reference
});
```

### std::function
A uniform container for storing callable objects
- static functions (`void foo(){}`)
- function objects (`operator ()`)
- lambdas (`[](){}`)

#### Declaration
```cpp
// return type int, arguments: int, int
std::function<int(int,int)> func;
```

#### Assignment

```cpp
int add(int a, int b){
    return a + b;
}
func = add;
```

```cpp
func = [](int a, int b){
    return a + b;
}
```

## MAIN
All C++ programs must contain a global function with the name `main`
- Program's entry point
- Invoked on startup

Possible Implementations:
```cpp
int main(); // without arguments
int main(int argc, char* argv[]); // with arguments
```

### Command Line Arguments
Command Line Arguments are provided when you start the program through a CommandLine.

#### Providing Arguments
```cpp
./rpg.exe Easy
```

#### Reading Arguments
```cpp
int main(int argc, char* argv[]){
    const char* difficulty;
    if(argc == 0){
        // no argument provided
        difficulty = "Medium";
    } else {
        // else, we pick the first argument
        difficulty = argv[0]; 
    }
}
```


### Exit Status
Main can return an exit status code
- these are not standardized
- generally, `0` means that all went well
- a default `return 0;` is added if you don't provide it

```cpp
int main(){
    printf("Hello, World");
    // implicit: return 0;
}
```