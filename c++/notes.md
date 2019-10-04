# C++ notes
##### notes on "A Tour of C++" by Bjarne Stroustrup

## 1.2.1 Hello, World!

```cpp
#include <iostream>  // include the declarations for the I/O stream library
using namespace std; // make names from std visible without std::

double square(double x){ // square a double precision floating-point number
    return x∗x;
}
void print_square(double x){
    cout << "the square of " << x << " is " << square(x) << "\n";
}
int main(){
    print_square(1.234); // pr int: the square of 1.234 is 1.52276
}
```

`std::cout << "Hello, World!\n"`\
The operator `<<` (‘‘put to’’) writes its second argument onto its first. In this
case, the string literal `"Hello, World!\n"` is written onto the standard output stream `std::cout`.
The `std::` specifies that the name `cout` is to be found in the standard-library namespace. `using namespace std` tells the compiler to take everything that's in the std namespace and dump it in the global namespace.

## 1.3 Functions

The semantics of argument passing are identical to the semantics of initialization. That
is, argument types are checked and implicit argument type conversion takes place when necessary. For example:
```cpp
double s2 = sqrt(2);       // call sqrt() with the argument double{2}
double s3 = sqrt("three"); // error : sqr t() requires an argument of type double
```
A function declaration may contain argument names. This can be a help to the reader of a program, but unless the declaration is also a function definition, the compiler simply ignores such
names. For example:
```cpp
double sqrt(double d); // return the square root of d
double square(double); // return the square of the argument
```
The type of a function consists of its return type and the sequence of its argument types. For example:
```cpp
double get(const vector<double>& vec, int index); // type: double(const vector<double>&,int)
```
A function can be a member of a class. For such a member function, the name of its class is also part of the function type. For example:
```cpp
char& String::operator[](int index);
```
If two functions are defined with the same name, but with different argument types, the compiler will choose the most appropriate function to invoke for each call. If two alternative functions could be called, but neither is better than the other, the call is deemed ambiguous and the compiler gives an error.

## 1.4 Types, Variables, and Arithmetic

Every name and every expression has a type that determines the operations that may be performed on it.\
A declaration is a statement that introduces an entity into the program. It specifies a type for the entity:
* A `type` defines a set of possible values and a set of operations (for an object).
* An `object` is some memory that holds a value of some type.
* A `value` is a set of bits interpreted according to a type.
* A `variable` is a named object.

