# 2 Conversion

## Implicit Type Conversion

```cpp
auto x = 3.142423434234L;
int y = x;
```

Use Braces!

```cpp
auto x = 3.142423434234L;
int y{x};           // WARNING
```

## Bool Conversion
Any type is implicitly convertible to `bool`

```cpp
if(5){}
if(new Player()){}
if('c'){}
if(0){}
```

## C-Style Casts
Don't use them!

```cpp
const char* readOnly;
// Here, we're losing the `const` part unintentionally!
auto asUnsigned = (unsigned char*) readOnly;
```

Use `reinterpret_cast` or `static_cast` instead

```cpp
// ERROR: cannot cast away `const`
auto asUnsigned = reinterpret_cast<unsigned char*>(readOnly);
```

## User-Defined Type Conversions
Make the explicit!
```cpp
struct ReadOnlyInt {
    // From int to ReadOnlyInt:
    explicit ReadOnlyInt(int val) : val{ val } { }
    // From ReadOnlyInt to int:
    explicit operator int() const {
        return val;
    }
private:
    const int val;
};
```