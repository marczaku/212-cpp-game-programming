# 1 User-Defined Literals

```cpp
#include <cstdio>

struct Temperature {
	long double value;
	long double getKelvin() { return value * 273.15; }
};

constexpr Temperature operator "" °C(long double deg) {
	return Temperature{ deg };
}

int main() {
	auto temp = 25.0°C;
    printf("The temperature is %f degrees Kelvin.\n", temp.getKelvin());
	return 0;
}
```