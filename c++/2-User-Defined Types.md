# _C++ notes_: 2 - User-Defined Types

##### notes on "A Tour of C++" by Bjarne Stroustrup

## 2.1 Introduction

The _built-in types_ are those that can be built from the fundamental types, the `const` modifier, and the declarator operators. Types built out of other types using C++'s abstraction mechanisms are instead called _user-defined_ types. They are referred to as _classes_ and _enumerations_.

## 2.2 Structures

```cpp
struct Vector {
  int sz;        // number of elements
  double∗ elem;  // pointer to elements
};

Vector v;               // a variable of type Vector can be defined like this
v.elem= new double[s];  // allocate an array of s doubles
```

The `new` operator allocates memory from the heap. Objects allocated on the heap are independent of the scope from which they are created and "live" until they are destroyed using the `delete` operator.

## 2.3 Classes

We have to distinguish between the interface to a type (to be used by all) and its implementation (which has access to the otherwise inaccessible data) by using the language mechanism called _class_. A class has a set of _members_, which can be data, function, or type members.\
The interface is defined by the `public` members of a class, and `private` members are accessible only through that interface. For example:

```cpp
class Vector {
 public:
  Vector(int s) : elem{new double[s]}, sz{s} {
  }                            // construct a Vector
  double& operator[](int i) {  // element access: subscripting
    return elem[i];
  }
  int size() {
    return sz;
  }

 private:
  double∗ elem;  // pointer to the elements
  int sz;        // the number of elements
};

Vector v(6);  // a Vector with 6 elements
```

The representation of a `Vector` (the members `elem` and `sz`) is accessible only through the interface provided by the `public` members: `Vector()`, `operator[]()`, and `size()`.\
The constructor initializes the `Vector` members using a member initializer list:

```cpp
:elem{new double[s]}, sz{s}
```

That is, we first initialize `elem` with a pointer to `s` elements of type `double` obtained from the heap. Then, we initialize `sz` to `s`.\
Access to elements is provided by a subscript function, called `operator[]` (returning a `double&`, allowing both reading and writing).

There is no fundamental difference between a `struct` and a `class`; a `struct` is simply a `class` with members `public` by default. For example, you can define constructors and other member functions for a `struct`.

## 2.4 Unions

A `union` is a `struct` in which all members are allocated at the same address so that the `union` occupies only as much space as its largest member. Naturally, a `union` can hold a value for only one member at a time.

```cpp
union Value {
  Node ∗p;
  int i;
};

struct Entry {  // the language doesn't keep track of which kind of value is held by a union, so the programmer must do that
  string name;
  Type t;
  Value v;  // use v.p if t==ptr; use v.i if t==num
};
```

Maintaining the correspondence between a _type field_ (here, `t`) and the type held in a `union` is error prone. To avoid errors, we can enforce that correspondence by encapsulating the union and the type field in a class and offer access only through member functions that use the union correctly.\
The standard library type, `variant`, can be used to eliminate most direct uses of unions:

```cpp
struct Entry {
  string name;
  variant<Node∗, int> v;
};

void f(Entry∗ pe) {
  if(holds_alternative<int>(pe−> v))  // does *pe hold an int?
    cout << get<int>(pe−> v);         // get the int
                                      // ...
}
```

## 2.5 Enumerations

In addition to classes, C++ supports a simple form of user-defined type for which we can enumerate the values:

```cpp
enum class Color { red, blue, green };
enum class Traffic_light { green, yellow, red };

Color col= Color::red;
Traffic_light light= Traffic_light::red;
```

The `class` after the `enum` specifies that an enumeration is strongly typed and that its enumerators are scoped. Being separate types, `enum class`es help prevent accidental misuses of constants. In particular, we cannot mix `Traffic_light` and `Color` values, or mix `Color` and integer values:

```cpp
Color x= red;                 // error : which red?
Color y= Traffic_light::red;  // error : that red is not a Color
int i= Color::red;            // error : Color ::red is not an int
Color c= 2;                   // initialization error: 2 is not a Color
```

Initialize an `enum` with a value from its underlying type (by default, that's `int`) is allowed, as is explicit conversion from the underlying type:

```cpp
Color x= Color{5};  // OK, but verbose
Color y{6};         // also OK
```

An enumeration is a user-defined type, so we can define operators for it:

```cpp
Traffic_light& operator++(Traffic_light& t) {  // prefix increment: ++
  switch(t) {
    case Traffic_light::green:
      return t= Traffic_light::yellow;
      // ...
  }
}

Traffic_light next= ++light;  // next becomes Traffic_light::yellow
```

Alternatively, you can remove the `class` from `enum class` to get a "plain" `enum`. The enumerators from a "plain" `enum` are entered into the same scope as the name of their `enum` and implicitly converts to their integer value:

```cpp
enum Color { red, green, blue };  // by default the integer values of enumerators start with 0
int col= green;                   // here col gets the value 1
```
