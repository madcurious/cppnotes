# Chapter 2 - User-Defined Types

## Structures

A structure is a container for multiple values. `struct` members may be accessed by `.`. through a name or a reference, and by `->` through a pointer.

```cpp
#include <iostream>

struct Vector {
  int size;
  double* elements;
};

void print_vector(const Vector* vector) {
  double* cursor = vector->elements;
  for (int i = 0; i < vector->size; ++i) {
    std::cout << *cursor << " ";
    ++cursor;
  }
  std::cout << "\n";
}

int main() {
  Vector vector;
  vector.size = 5;
  vector.elements = new double[5];
  
  for (int i = 0; i < vector.size; ++i) {
    vector.elements[i] = 9-i;
  }
  print_vector(&vector); // 9 8 7 6 5
  
  delete[] vector.elements;
  
  // may still print the same array but OS may overwrite memory anytime
  print_vector(&vector);
  
  return 0;
}
```

Unlike C structures, C++ structures can have member functions, constructors, and destructors.

## Classes

Classes are similar to structures, except that they also grant the ability to separate `public` interfaces from `private` implementations. With structures, everything is public by default.

```cpp
#include <iostream>

class Vector {
public:
  Vector(int size) :size{size}, elements{new double[size]} {} // member initialier list
  double& operator[](int index) {
    return elements[index];
  }
  ~Vector() { // destructor
    delete[] elements;
  }
private:
  int size;
  double* elements;
};

int main() {
  int size = 6;
  Vector vector(size);
  for (int i = 0; i < size; ++i) {
    vector[i] = 10 - i;
  }
  for (int i = 0; i < size; ++i) {
    std::cout << vector[i] << " "; // prints "10 9 8 7 6 5 "
  }
  std::cout << "\n";
  
  return 0;

  // no need to call delete[] on vector.elements
  // vector is auto-deleted after this scope due to destructor
}
```

Constructors and destructors help the programmer avoid errors and memory leaks by avoiding direct calls to `new` and `delete`. This technique is called *Resource Allocation is Initialization* or RAII.

## Enumerations

Enumerations allow you to choose one of many possible values.

```cpp
#include <iostream>

enum ValueType {
  string, number
};

struct Entry {
  ValueType valueType;
  std::string text;
  int number;
};

void print_entry(Entry* entry) {
  if (entry->valueType == string) {
    std::cout << "text: " << entry->text << "\n";
  } else {
    std::cout << "number: " << entry->number << "\n";
  }
}

int main() {
  Entry entry;
  entry.text = "Hello world!";
  entry.number = -5;
  entry.valueType = number;
  print_entry(&entry);
  return 0;
}
```

There are two ways to define an enumeration: as a plain `enum` (as above) and as an `enum class`.

Plain `enums` implicitly have `int` as the underlying type of every enumerator (i.e. enum "case") starting with `0` and increasing by 1 for each additional case. Cases are also scoped wherever the enum is declared, which can then create name clashes.

```cpp
#include <iostream>

enum ValueType {
  string, number
};

enum InstrumentType {
  percussion, wind, string // compile error: redefinition of 'string'
};

int main() {
  ValueType t{string};
  int i = t;
  std::cout << t << "\n"; // prints 0
  return 0;
}
```

Enum classes, however, do not implicitly convert to ints, have cases that are scoped within the enum, and can have explicit declarations of the underlying integral type.

```cpp
#include <iostream>

enum class ValueType {
  string, number
};

// with explicit integral type
enum class InstrumentType: char {
  percussion = 'p', wind = 'w', string = 'w'
};

int main() {
  // enum cases now require full namespace
  ValueType valueType{ValueType::number};

  // static_cast is a typecasting function
  std::cout << static_cast<char>(InstrumentType::wind) << "\n";
  return 0;
}
```

## Unions and Variants

A union is a struct in which all members are allocated on the same shared address. Unions only hold one value at a time and occupy as much space as the largest type among its members. Typically, unions are used in conjunction with enumeration types to represent values that are associated to enum cases.

```cpp
#include <iostream>

enum class ValueType {
  string, number
};

union Value {
  std::string* text;
  int number;
};

struct Entry {
  ValueType valueType;
  Value value;
};

void print_entry(Entry* entry) {
  if (entry->valueType == ValueType::string) {
    std::cout << "text: " << *entry->value.text << "\n";
  } else {
    std::cout << "number: " << entry->value.number << "\n";
  }
}

int main() {
  std::string string = "Hello world!";
  Entry entry;
  entry.valueType = ValueType::string;
  entry.value = Value{.text=&string}; // alternately: Value{.number=-5}
  print_entry(&entry); // prints "text: Hello world!"
  return 0;
}
```

Maintaining the correspondence between an enumeration and a union is error-prone. It is suggested that the two values are wrapped in a class that only offers access through public interfaces.

Alternatively, the standard library type `variant` can be used to represent unions in a safe manner. See the rewrite of `Entry` below, and note the use of `holds_alternative()` and `get()` within the `std` namespace.

```cpp
#include <iostream>
#include <variant>

struct Entry {
  std::variant<std::string*, int> value;
};

void print_entry(Entry* entry) {
  if (std::holds_alternative<std::string*>(entry->value)) {
    std::cout << "text: " << *std::get<std::string*>(entry->value) << "\n";
  } else {
    std::cout << "number: " << std::get<int>(entry->value) << "\n";
  }
}

int main() {
  std::string string = "Hello world!";
  Entry entry;
  entry.value = &string;
  print_entry(&entry); // prints "text: Hello world!"

  entry.value = 3;
  print_entry(&entry); // prints "number: 3"

  return 0;
}
```