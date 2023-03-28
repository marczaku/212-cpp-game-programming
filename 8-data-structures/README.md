# 8 Data Structures

## Tribool
- `boost::logic::tribool`
- 3 states: `true`, `false`, `boost::logic::indeterminate`

## Optional
- `std::optional<Loot>`
- used to say that a function might not return a value

## Pair
- `std::pair<Player, Item>`
- used to store two objects in one variable

## Tuple
- `std::tuple<Player, Clan, Score>`
- used to store any number of objects in one variable

## Any
- `std::any`
- used to store any object type-safely in a variable

## Variant
- `std::variant<SystemMessage, PlayerMessage>`
- used to store either one of the provided types in a variable

## Date and Time
- `boost::gregorian::date`
- `std::chrono::system_clock`
- `std::chrono::time_point`
- `std::chrono::duration`
- `std::chrono::high_resolution_time`
- `std::chrono::nanoseconds`

## Math
- `#include <cmath>`
- `abs`, `fmod`, `min`, `max`, `exp`, `log`, `pow`, `sqrt`, `sin`, `cos`, `floor`, `round`, ...
- `half`, `two_thirds`, `pi`, `two_i`, `radian`, `degree`, `e`, ...

## Random
```cpp
// set random engine + seed
std::mt19937_64 mt_engine{91586};
// set distribution type and range
std::uniform_int_distribution<int> int_d{0, 10};
// roll
int_d(mt_engine);
```

## Numeric Limits
- `std::numeric_limits<int>::min()` etc.