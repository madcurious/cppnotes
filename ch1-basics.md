# Chapter 1 - The Basics

## Functions

### General form

`returntype name(params)`

```cpp
Elem* next_elem()       // return a pointer to an `Elem`, no arguments
void exit(int)          // return nothing, take an `int` argument
double sqrt(double d)   // return `double`, take a `double` named `d`
```

### Type

The type of a function is its return type and the arguments that it takes.

```cpp
// type: double(const vector<double>&,int)
double get(const vector<double>& vec, int index);
```

If a function is a member of a class, the class name is included in the function type.

```cpp
// type: char& String::(int)
char& String::operator[](int index);
```

### Overloading

Function *overloading* refers to defining functions with the same name but with different argument types. The C++ compiler chooses the appropriate one when a call is made.

```cpp
void print(int);     // takes an integer argument
void print(double);  // takes a floating-point argument
void print(string);  // takes a string argument
```

## Types

* `bool` - either `true` or `false`
* `char` - e.g. `'a'`
* `int`
* `double`

The following can be used as modifiers to numeric types, but if used alone, a type of `int` is assumed:

* `unsigned`
* `short`
* `long`

The size of a type may vary per machine and can be obtained with the `sizeof` operator (e.g., `sizeof(int)`).

## Operators

### Arithmetic

```cpp
x+y     // plus
+x      // unary plus
x−y     // minus
−x      // unary minus
x*y     // multiply
x/y     // divide
x%y     // remainder (modulus) for integers
```

### Comparison

```cpp
x==y     // equal
x!=y     // not equal
x<y      // less than
x>y      // greater than
x<=y     // less than or equal
x>=y     // greater than or equal
```

### Logical

```cpp
x&y      // bitwise and
x|y      // bitwise or
x^y      // bitwise exclusive or
~x       // bitwise complement
x&&y     // logical and
x||y     // logical or
!x       // logical not (negation)
```

### Others

* Arithmetic-assignment: `+=`, `-=`, `*=`, `/=`, `%=`
* Increment and decrement: `++x`, `--x`

## Initialization

* Initialization of objects can be done via `=` and `{}` operators.

```cpp
double d1 = 3.14;
double d2 {3.14};
```

* The difference between `=` and `{}` is that `{}` does not allow *narrowing conversions* or conversions that lose information, e.g. when you assign a `double` literal to an `int` variable.

```cpp
int i1 = 3.14;    // allowed
int i2 {3.14};    // compile error
```

* Use the `auto` keyword to make the compiler infer the type of a variable based on its initial value. The type cannot be changed once it has been initialized.

```cpp
auto d = 3.14;    // infers double
d = 4;            // d is still a double
d = "string";     // compile error
```

* Objects created with the `new` keyword live until destroyed with the `delete` keyword.

* Initialization is different from assignment. Initialization makes an uninitialized piece of memory into a valid object; an assignment assigns or copies values into an initialized object. For almost all types, reading/writing from unintialized objects is undefined behavior, and uninitialized references throw compiler errors.

```cpp
int i;
std::cout << i << "\n"; // compiles, i prints 0, but this is undefined behavior

int& ref; // compiler error
ref = i;
```

## Constants

* Use `const` to define immutable values that must be evaluated during run time.

* Use `constexpr` to define immutable values that must be evaluated during compile time.

```cpp
int var = 36;
const double sqrt_var = sqrt(var); // var is non-constant
constexpr double pi = 3.1415;
constexpr double sqrt_var2 = sqrt(var); // compile error
```

* Functions can be used in constant expressions by also using the `constexpr` keyword.

```cpp
constexpr double square(double x) {
  return x*x;
}
constexpr double squared_pi = square(pi);
constexpr double squared_var = square(var); // compile error
```

## Pointers, Arrays, and References

`*`, `&`, and `[]` are called *declarator operators* when used in declarations.

Pointers hold memory addresses of their type. Pointers are declared with `*` and addresses of objects are retrieved by `&`. To get the value that a pointer points to, dereference by `*`.

```cpp
char v[6];           // array of 6 characters
char* p = &v[3];     // p points to v's fourth element
char x = *p;         // *p is the object that p points to
```

References, declared with `&`, are conceptually similar to pointers, except that the value referred to by the reference can be accessed directly without using `*`. References also cannot be made to refer to a different object once initialized.

References are most useful where we do not wish to make a copy of a value, such as array iteration or function arguments.

```cpp
// reference used as iterator
int nums[] {-1, 3, 2, -5, 4, -3};
for (int& i : nums) {
  std::cout << i << "\n";
}

// reference in function args, ensures sorting v instead of an internal copy
void sort(vector<double>& v);
```

### Null Pointer

Use `nullptr` to represent the notion of nullity. Must be assigned on pointer types.

```cpp
int countOccurrencesOfIntegerInZeroTerminatedArray(const int* cursor, int number) {
  if (cursor == nullptr) {
    return 0;
  }
  int count = 0;
  while (*cursor != 0) {
    if (*cursor == number) {
      ++count;
    }
    ++cursor;
  }
  return count;
}

int main(int argc, const char * argv[]) {
  int array[] {1, 2, 2, 2, 1, 2, 1, 2, 2, 1, 0};
  int count1 = countOccurrencesOfIntegerInZeroTerminatedArray(array, 1);
  std::cout << count1 << "\n"; // 4
  int count2 = countOccurrencesOfIntegerInZeroTerminatedArray(nullptr, 1);
  std::cout << count2 << "\n"; // 0
  return 0;
}
```

## Conditionals, `switch`, and loops

Conditionals can be expressed via `if`-`else` statements. Conditions must be within parentheses.

```cpp
if (true) {
  // ...
} else {
  // ...
}
```

If statements can perform value bindings within the condition before the condition itself.

```cpp
if (auto count = array.size(); count != 0) {
  // ...
}

// Or, simply:
if (auto count = array.size()) {
  // ...
}
```

Switch statements test for equality against a sequence of cases. If no cases match, the `default` case is executed. If no `default` case is stated, no action is taken. Cases must explicitly `break` or code execution will fall through the next case.

```cpp
char c = 'a';
switch (c) {
  case 'a':
  case 'e':
  case 'i':
  case 'o':
  case 'u':
    std::cout << "vowel\n";
    break;
  default:
    std::cout << "consonant\n";
}
```

Loops in C++ are `for`, range-`for` ("for-in" in other languages), `while`, and `do-while`.

```cpp
int nums[] {5, -7, 1, 4, -3};
int length = sizeof(nums) / sizeof(int);

// regular for loop
std::cout << "regular for: ";
for (int i = 0; i < length; ++i) {
  std::cout << nums[i] << " ";
}
std::cout << "\n";

// range-for
std::cout << "range-for: ";
for (int& i : nums) {
  std::cout << i << " ";
}
std::cout << "\n";

// while loop
std::cout << "while: ";
int i = 0;
while (i < length) {
  std::cout << nums[i] << " ";
  ++i;
}
std::cout << "\n";

// do-while
std::cout << "do-while: ";
i = 0;
do {
  std::cout << nums[i] << " ";
  ++i;
} while (i < length);
std::cout << "\n";

// regular for: 5 -7 1 4 -3 
// range-for: 5 -7 1 4 -3 
// while: 5 -7 1 4 -3 
// do-while: 5 -7 1 4 -3 
```