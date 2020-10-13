##### notes on "A Tour of C++" by Bjarne Stroustrup

# _C++ notes_: 3 - Modularity

## 3.1 Introduction

The first and most important step is to distinguish between the interface to a part and its implementation. At the language level, C++ represents interfaces by declarations. A _declaration_ specifies all that's needed to use a function or a type:

```cpp
double sqrt(double);  // the square root function takes a double and returns a double

class Vector {
 public:
  Vector(int s);
  double& operator[](int i);
  int size();

 private:
  double∗ elem;  // elem points to an array of sz doubles
  int sz;
};
```

We might like the representation of `Vector` to be "elsewhere":

```cpp
Vector::Vector(int s)  // definition of the constructor
    : elem{new double[s]}, sz{s} {
}  // initialize members

double& Vector::operator[](int i) {  // definition of subscripting
  return elem[i];
}

int Vector::size() {  // definition of size()
  return sz;
}
```

There can be many declarations for an entity, such as a function, but only one definition.

## 3.2 Separate Compilation

C++ supports a notion of separate compilation where user code sees only declarations of the types and functions used. A library is often a collection of separately compiled code fragments (e.g., functions).
Typically, we place the declarations that specify the interface to a module in a file called a _header file_.\
To help the compiler ensure consistency, the `.cpp` file providing the implementation of `Vector` will also include the `.h` file providing its interface:

```cpp
// Vector.cpp:

#include "Vector.h"  // get Vector's interface
```

The best approach to program organization is to think of the program as a set of modules with well-defined dependencies, represent that modularity logically through language features, and then exploit the modularity physically through files for effective separate compilation. A `.cpp` file that is compiled by itself (including the `h` files it `#includes`) is called a _translation unit_. A program can consist of many thousand translation units.

## 3.3 Modules (C++20)

The language feature, called `modules` is not yet ISO C++, but it is an ISO Technical Specification [ModulesTS] and will be part of C++20.\
Consider how to express the `Vector` and `sqrt_sum()` example from **3.2** using `modules`:

```cpp
// file Vector.cpp:

module;  // this compilation will define a module

// ... here we put stuff that Vector might need for its implementation ...

export module Vector;  // defining the module called "Vector"

export class Vector {
 public:
  Vector(int s);
  double& operator[](int i);
  int size();

 private:
  double∗ elem;  // elem points to an array of sz doubles
  int sz;
};

Vector::Vector(int s) : elem{new double[s]}, sz{s} {
}  // initialize members

double& Vector::operator[](int i) {
  return elem[i];
}

int Vector::size() {
  return sz;
}

export int size(const Vector& v) {
  return v.size();
}
```

This defines a `module` called `Vector`, which exports the class `Vector`, all its member functions, and the non-member function `size()`.\
The way we use this `module` is to `import` it where we need it:

```cpp
// file user.cpp:

import Vector;    // get Vector's interface
#include <cmath>  // get the standard-library math function interface including sqrt()

double sqrt_sum(Vector& v) {
  double sum= 0;
  for(int i= 0; i != v.size(); ++i)
    sum+= std::sqrt(v[i]);  // sum of square roots
  return sum;
}
```

The differences between headers and modules are not just syntactic:

* A module is compiled once only (rather than in each translation unit in which it is used).
* Two modules can be `import`ed in either order without changing their meaning.
* If you import something into a module, users of your module do not implicitly gain access to (and are not bothered by) what you imported: `import` is not transitive.

## 3.4 Namespaces

C++ offers _namespaces_ as a mechanism for expressing that some declarations belong together and that their names shouldn't clash with other names:

```cpp
namespace My_code {
class complex {
  // ...
};
complex sqrt(complex);
// ...
int main();
}  // namespace My_code

int My_code::main() {
  complex z{1, 2};  // My_code::complex
  auto z2= sqrt(z);
  std::cout << '{' << z2.real() << ',' << z2.imag() << "}\n";
  // ...
}

int main() {
  return My_code::main();
}
```

By putting my code into the namespace `My_code`, I make sure that my names do not conflict with the standard-library names in namespace `std`, as the standard library does already provide support for `complex` arithmetic.

The simplest way to access a name in another namespace is to qualify it with the namespace name (e.g., `std::cout` and `My_code::main`). The "real `main()`" is defined in the global namespace, that is, not local to a defined namespace, class, or function. If repeatedly qualifying a name becomes tedious or distracting, we can bring the name into a scope with a `using`-declaration:

```cpp
void my_code(vector<int>& x, vector<int>& y) {
  using std::swap;  // use the standard-library swap
  // ...
  swap(x, y);         // std::swap()
  other::swap(x, y);  // some other swap()
  // ...
}
```

A `using`-declaration makes a name from a namespace usable as if it was declared in the scope in which it appears. After using `std::swap`, it is exactly as if `swap` had been declared in `my_code()`.

By using a `using`-directive, we lose the ability to selectively use names from that namespace, so this facility should be used carefully, usually for a library that's pervasive in an application (e.g., `std`) or during a transition for an application that didn't use `namespace`s.

## 3.5 Error Handling

C++ provides a few features to simplify our programming and limit our opportunities for mistakes: the major tool is the type system itself, that also increases the compiler's chances of catching errors.

### Exceptions

Consider again the `Vector` example. `Vector::operator[]()` can detect an attempted out-of-range access and throw an `out_of_range` exception:

```cpp
double& Vector::operator[](int i) {
  if(i < 0 || size() <= i)
    throw out_of_range{"Vector::operator[]"};
  return elem[i];
}

void f(Vector& v) {
  // ...
  try {                         // exceptions here are handled by the handler defined below
    v[v.size()]= 7;             // try to access beyond the end of v
  } catch(out_of_range& err) {  // oops: out_of_range error
    // ... handle range error ...
    cerr << err.what() << '\n';
  }
  // ...
}
```

The `out_of_range` type is defined in the standard library (in `<stdexcept>`) and is in fact used by some standard-library container access functions. The exception is caught by reference to avoid copying and used the `what()` function to print the error message put into it at the `throw`-point.

A function that should never throw an exception can be declared `noexcept`:

```cpp
void user(int sz) noexcept {
  // ...
}
```

If all good intent and planning fails, so that `user()` still throws, `std::terminate()` is called to immediately terminate the program.\
Often, a function has no way of completing its assigned task after an exception is thrown. Then, "handling" an exception means doing some minimal local cleanup and rethrowing the exception:

```cpp
catch(std::length_error&) {
  // ... cleanup ...
  throw;  // rethrow
}
```

### Contracts

There is currently no general and standard way of writing optional run-time tests of invariants, preconditions, etc, but the standard library offers the **debug** macro, `assert()`, to assert that a condition must hold at run time:

```cpp
void f(const char∗ p) {
  assert(p != nullptr);  // p must not be the nullptr
  // ...
}
```

### Static Assertions

The `static_assert` mechanism can be used for anything that can be expressed in terms of constant expressions:

```cpp
constexpr double C= 299792.458;  // km/s
void f(double speed) {
  constexpr double local_max= 160.0 / (60∗60);          // 160 km/h == 160.0/(60*60) km/s
  static_assert(speed < C, "can't go that fast");      // error: speed must be a constant
  static_assert(local_max < C, "can't go that fast");  // OK
  // ...
}
```

The default message is typically the source location of the `static_assert` plus a character representation of the asserted predicate. Their most important uses come when we make assertions about types used as parameters in generic programming

## 3.6 Function Arguments and Return Values

### Argument Passing

If we want to pass by reference for performance reasons but don't need to modify the argument, we pass-by-`const`-reference.

We can specify a *default function argument*, it's a notationally simpler alternative to overloading

```cpp
void print(int value, int base= 10);  // print value in base "base"
print(x, 16);                         // hexadecimal
print(x);                             // use the default: decimal
```

### Value Return

We return "by reference" only when we want to grant a caller access to something that is not local to the function, alternatively, we a move constructor.

The return type of a function can be deduced from its return value with `auto`, but caution is advised, as a deduced type does not offer a stable interface: a
change to the implementation of the function (or lambda) can change the type.

### Structured Binding

```cpp
struct Entry {
  string name;
  int value;
};

Entry read_entry(istream& is) {  // naive read function (for a better version, see 10.5)
  string s;
  int i;
  is >> s >> i;
  return {s, i};
}
```

`{s,i}` is used to construct the `Entry` return value. Similarly, we can "unpack" an `Entry`'s members into local variables:

```cpp
auto [n, v]= read_entry(is);
```

The `auto [n,v]` declares two local variables `n` and `v` with their types deduced from `read_entry()`'s return type. This mechanism for giving local names to members of a class object is called *structured binding*.

As usual, we can decorate `auto` with `const` and `&`:

```cpp
map<string, int> m;
// ... fill m ...
for(const auto [key, value] : m) {
  cout << "{" << key "," << value << "}\n";
}

void incr(map<string, int>& m) {  // increment the value of each element of m
  for(auto& [key, value] : m) {
    ++value;
  }
}
```

There will not be any difference in the object code quality compared to explicitly using a composite object; the use of structured binding is all about how best to express an idea.

It is also possible to handle classes where access is through member functions.
