# 3 Constant Expressions

Constant Expressions are awesome, because they can be evaluated at compile time

Conditions
- all arguments provided must be compile-time-constants
- your function must be marked `constexpr`

Benefits
- zero runtime overhead

In the example below, 

```cpp
#include <cstdio>

constexpr int factorial(int n) {
retu    rn n <= 1 ? 1 : (n * factorial(n - 1));
}

int main() {
    // this will be replaced with `const int fact5 = 120;`
    constexpr int fact5 = factorial(5);

    printf("5! = %d", fact5);

    return 0;
}
```