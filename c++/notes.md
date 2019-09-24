# C++ notes
##### notes on "A Tour of C++" by Bjarne Stroustrup

## Hello, World!

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
The `std::` specifies that the name `cout` is to be found in the standard-library namespace./
You can leave out the `std::` when discussing standard features


