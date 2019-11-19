# _C++ notes_: 3 - Modularity
##### notes on "A Tour of C++" by Bjarne Stroustrup

## 3.1 Introduction

The first and most important step is to distinguish between the interface to a part and its implementation. At the language level, C++ represents interfaces by declarations. A _declaration_ specifies all that's needed to use a function or a type:
```cpp
double sqrt(double); // the square root function takes a double and returns a double

class Vector {
public:
    Vector(int s);
    double& operator[](int i);
    int size();
private:
    double∗ elem;    // elem points to an array of sz doubles
    int sz;
};
```
We might like the representation of `Vector` to be "elsewhere":
```cpp
Vector::Vector(int s)               // definition of the constructor
    :elem{new double[s]}, sz{s} {}  // initialize members

double& Vector::operator[](int i) { // definition of subscripting
    return elem[i];
}

int Vector::size() {               // definition of size()
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

#include "Vector.h" // get Vector's interface
```
The best approach to program organization is to think of the program as a set of modules with well-defined dependencies, represent that modularity logically through language features, and then exploit the modularity physically through files for effective separate compilation. A `.cpp` file that is compiled by itself (including the `h` files it `#includes`) is called a _translation unit_. A program can consist of many thousand translation units.

## 3.3 Modules (C++20)

The language feature, called `modules` is not yet ISO C++, but it is an ISO Technical Specification [ModulesTS] and will be part of C++20.\
Consider how to express the `Vector` and `sqrt_sum()` example from **3.2** using `modules`:
```cpp
// file Vector.cpp:

module;                             // this compilation will define a module

// ... here we put stuff that Vector might need for its implementation ...

export module Vector;               // defining the module called "Vector"

export class Vector {
public:
Vector(int s);
    double& operator[](int i);
    int size();
private:
    double∗ elem;                   // elem points to an array of sz doubles
    int sz;
};

Vector::Vector(int s)
    :elem{new double[s]}, sz{s} {}  // initialize members

double& Vector::operator[](int i) {
    return elem[i];
}

int Vector::size() {
    return sz;
}

export int size(const Vector& v) { return v.size(); }
```
This defines a `module` called `Vector`, which exports the class `Vector`, all its member functions, and the non-member function `size()`.\
The way we use this `module` is to `import` it where we need it:
```cpp
// file user.cpp:

import Vector;      // get Vector's interface
#include <cmath>    // get the standard-library math function interface including sqrt()

double sqrt_sum(Vector& v) {
    double sum = 0;
    for (int i=0; i!=v.size(); ++i)
        sum+=std::sqrt(v[i]);  // sum of square roots
    return sum;
}
```
The differences between headers and modules are not just syntactic:
* A module is compiled once only (rather than in each translation unit in which it is used).
* Two modules can be `import`ed in either order without changing their meaning.
* If you import something into a module, users of your module do not implicitly gain access to (and are not bothered by) what you imported: `import` is not transitive.

## Namespaces

