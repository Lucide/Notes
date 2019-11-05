# _C++ notes_: 1 - The Basics
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
The operator `<<` ("put to") writes its second argument onto its first. In this case, the string literal `"Hello, World!\n"` is written onto the standard output stream `std::cout`.\
The `std::` specifies that the name `cout` is to be found in the standard-library namespace. `using namespace std` tells the compiler to take everything that's in the `std` namespace and dump it in the global namespace.

## 1.3 Functions

The semantics of argument passing are identical to the semantics of initialization. That is, argument types are checked and implicit argument type conversion takes place when necessary:
```cpp
double s2 = sqrt(2);       // call sqrt() with the argument double{2}
double s3 = sqrt("three"); // error : sqr t() requires an argument of type double
```
A function declaration may contain argument names. This can be a help to the reader of a program, but unless the declaration is also a function definition, the compiler simply ignores such names:
```cpp
double sqrt(double d); // return the square root of d
double square(double); // return the square of the argument
```
The type of a function consists of its return type and the sequence of its argument types:
```cpp
double get(const vector<double>& vec, int index); // type: double(const vector<double>&,int)
```
A function can be a member of a class. For such a member function, the name of its class is also part of the function type:
```cpp
char& String::operator[](int index);
```
If two functions are defined with the same name, but with different argument types, the compiler will choose the most appropriate function to invoke for each call. If two alternative functions could be called, but neither is better than the other, the call is deemed ambiguous and the compiler gives an error.

## 1.4 Types, Variables, and Arithmetic

Every name and every expression has a type that determines the operations that may be performed on it.\
A declaration is a statement that introduces an entity into the program. It specifies a type for the entity:
* A `type` defines a set of possible values and a set of operations (for an object)
* An `object` is some memory that holds a value of some type
* A `value` is a set of bits interpreted according to a type
* A `variable` is a named object

The arithmetic operators can be used for appropriate combinations of the fundamental types:
* `x+y` plus
* `+x` unary plus
* `x−y` minus
* `−x` unary minus
* ...

Bitwise logical operator yields a result of the operand type for which the operation has been performed on each bit. The logical operators `&&` and `||` simply return `true` or `false` depending on the values of their operands.
* `xˆy` bitwise exclusive or
* `˜x` bitwise complement
* ...

In assignments and in arithmetic operations, C++ performs all meaningful conversions between the basic types so that they can be mixed freely:
```cpp
void some_function(){ // function that doesn't return a value
    double d = 2.2;   // initialize floating-point number
    int     i = 7;    // initialize integer
    d =d+i;           //assign sum to d
    i = d∗i;          //assign product to i; beware: truncating the double d*i to an int
}
```
The conversions used in expressions are called the [_usual arithmetic conversions_](https://docs.microsoft.com/en-us/cpp/cpp/standard-conversions) and aim to ensure that expressions are computed at the highest precision of its operands. For example, an addition of a `double` and an `int` is calculated using double-precision floating-point arithmetic.\
The order of evaluation of function arguments is unspecified.

### Initialization
C++ offers a variety of notations for expressing initialization:
```cpp
double d1 = 2.3;              // initialize d1 to 2.3
double d2 {2.3};              // initialize d2 to 2.3
double d3 = {2.3};            // initialize d3 to 2.3 (the = is optional with { ... })
complex<double> z = 1;        // a complex number with double-precision floating-point scalars
complex<double> z2 {d1,d2};
complex<double> z3 = {d1,d2}; // the = is optional with { ... }
vector<int> v {1,2,3,4,5,6};  // a vector of ints
```
The `=` form is traditional and dates back to C, but if in doubt, use the general `{}`-list form, it saves you from conversions that lose information:
```cpp
int i1 = 7.8; // i1 becomes 7 (surprise?)
int i2 {7.8}; // error : floating-point to integer conversion
```
_Narrowing conversions_ (conversions that lose information), such as `double` to `int` and `int` to `char`, are allowed and implicitly applied when you use `=` (but not when you use `{}`).

A constant cannot be left uninitialized, and a variable shouldn't be too. User defined types (such as `string`, `vector`, `Matrix`, `Motor_controller`, ...) can be defined to be implicitly initialized.

When defining a variable, you don't need to state its type explicitly when it can be deduced from the initializer:
```cpp
auto b = true;    // a bool
auto ch = 'x';    // a char
auto i = 123;     // an int
auto d = 1.2;     // a double
auto z = sqrt(y); // z has the type of whatever sqr t(y) retur ns
auto bb {true};   // bb is a bool
```
With `auto`, we tend to use the `=` because there is no potentially troublesome type conversion involved, but you can also use `{}` initialization for better consistency.\
We use `auto` where we don't have a specific reason to mention the type explicitly. As when:
* The definition is in a large scope where we want to make the type clearly
visible to readers of our code.
* We want to be explicit about a variable's range or precision (e.g., `double` rather than `float`).

Using `auto`, we avoid redundancy and writing long type names. This is especially important in generic programming where the exact type of an object can be hard for the programmer to know.

## 1.5 Scope and Lifetime

A declaration introduces its name into a scope:
* _Local scope_: a name declared in a function or lambda is called a _local name_. Its scope extends from its declaration to the end of the block. A _block_ is delimited by a `{ }` pair. Function argument names are considered local names.
* _Class scope_: a name is called a _member name_ (or a _class member name_) if it is defined in a class, outside any function, lambda, or `enum class`. Its scope extends from the opening `{` of its enclosing declaration to the end of that declaration.
* _Namespace scope_: a name is called a _namespace member name_ if it is defined in a namespace outside any function, lambda, class, or `enum class`. Its scope extends from the point of declaration to the end of its namespace.

A name not declared inside any other construct is called a _global name_ and is said to be in the _global namespace_.

In addition, we can have objects without names, such as temporaries and objects created using new.

An object must be constructed (initialized) before it is used and will be destroyed at the end of its scope (at the end of the program for a namespace object). An object created by `new` "lives" until destroyed by `delete`.

## 1.6 Constants

C++ supports two notions of immutability:
* `const`: meaning roughly "I promise not to change this value". This is used primarily to specify interfaces so that data can be passed to functions using pointers and references without fear of it being modified. The compiler enforces the promise made by `const`. The value of a `const` can be calculated at run time.
* `constexpr`: meaning roughly "to be evaluated at compile time". This is used primarily to specify constants, to allow placement of data in read-only memory (where it is unlikely to be corrupted), and for performance. The value of a `constexpr` must be calculated by the compiler.

For a function to be usable in a constant expression, that is, in an expression that will be evaluated by the compiler, it must be defined `constexpr`:
```cpp
constexpr double square(double x) {
    return x∗x;
}

constexpr double max1 = 1.4∗square(17);  // OK 1.4*square(17) is a constant expression
constexpr double max2 = 1.4∗square(var); // error : var is not a constant expression
const double max3 = 1.4∗square(var);     //OK, may be evaluated at run time
```
A `constexpr` function can be used for non-constant arguments, but when that is done the result is not a constant expression.\
To be `constexpr`, a function must be rather simple and cannot have side effects and can only use information passed to it as arguments. In particular, it cannot modify non-local variables, but it can have loops and use its own local variables. For example:
```cpp
constexpr double nth(double x, int n){ // assume 0<=n
    double res = 1;
    int i = 0;

    while (i<n) { // while-loop: do while the condition is true
        res∗=x;
        ++i;
    }
    return res;
}
```
In a few places, constant expressions are required by language rules (e.g., array bounds, case labels, template value arguments, and constants declared using `constexpr`).

## 1.7 Pointers, Arrays, and References

The size of an array must be a constant expression.

C++ offers a simpler `for`-statement, called a range-`for`-statement, for loops that traverse a sequence in the simplest way:
```cpp
void print(){
    int v[] = {0,1,2,3,4,5,6,7,8,9}; // we don't have to specify an array bound when we initialize it with a list

    for (auto x : v) // for each x in v
        cout << x << '\n';
    for (auto x : {10,21,32,43,54,65})
        cout << x << '\n';
    // ...
}
```
The range-`for`-statement can be used for any sequence of elements.\
If we didn't want to copy the values from `v` into the variable `x`, but rather just have `x` refer to an element, we could write:
```cpp
void increment(){
    int v[] = {0,1,2,3,4,5,6,7,8,9};

    for (auto& x : v) // add 1 to each x in v
        ++x;
    // ...
}
```
In a declaration, the unary suffix `&` means "reference to". A reference is similar to a pointer, except that you don't need to use a prefix `∗` to access the value referred to by the reference. Also, a reference cannot be made to refer to a different object after its initialization.

References are particularly useful for specifying function arguments. For example:
```cpp
void sort(vector<double>& v); // sort v (v is a vector of doubles)
```
By using a reference, we ensure that for a call `sort(my_vec)`, we do not copy `my_vec` and that it really is `my_vec` that is sorted and not a copy of it.
When we don't want to modify an argument but still don't want the cost of copying, we use a `const` reference:
```cpp
double sum(const vector<double>&);
```

When used in declarations, operators (such as `&`, `∗`, and `[ ]`) are called _declarator operators_.

A declaration can appear anywhere a statement can. Like a `for`-statement, an `if`-statement can introduce a variable and test it:
```cpp
void do_something(vector<int>& v){
    if (auto n = v.siz e(); n!=0) {
    // ... we get here if n!=0 ...
    }
    // ...
}
```
The integer `n` is defined for use within the `if`-statement , initialized with `v.size()`, and immediately tested by the `n!=0` condition after the semicolon. You can also leave out the explicit mention of the condition, to test a variable against `0` (or the `nullptr`):
```cpp
if (auto n = v.siz e()) {
    // ... we get here if n!=0 ...
}
```

### Pointers
In older code, `0` or `NULL` is typically used instead of `nullptr`. However, using `nullptr` eliminates potential confusion between integers (such as `0` or `NULL`) and pointers (such as `nullptr`).\
There is no "null reference". A reference must refer to a valid object (and implementations assume that it does). There are obscure and clever ways to violate that rule; don't do that.

### Initialization
Initialization differs from assignment. In general, for an assignment to work correctly, the assigned-to object must have a value. On the other hand, the task of initialization is to make an uninitialized piece of memory into a valid object.\
The distinction between initialization and assignment is also crucial to many user-defined types, such as `string` and `vector`, where an assigned-to object owns a resource that needs to eventually be released.

