# _CMake_
##### notes on "How to CMake Good" by [vector-of-bool on YouTube](https://www.youtube.com/channel/UCkYGy96LXk3g-d6kP22aSDA)

## Note
I highly suggest reading the work found [here](https://cliutils.gitlab.io/modern-cmake/), it has by far the best explanation and exposition i could find on the subject.

## How to run CMake

There are three ways to run CMake:
* **CMAke CLI** with `cmake [path to source]`\
  runs a CMake configuration and stores the files in the working folder
* **curses CMake** with `ccmake [path to source]`\
  opens a text-based GUI to interactively edit che configuration, it stores the files in the working folder
* **CMake GUI** with `cmake-gui (path to build)`\
  opens a GUI to manage CMake configurations

You can also pass the path to an already configured build directory.

## A simple CMake project

An example of a minimal CMake project called `TestProject`, composed of a source file `main.c` and a build directory `build`.

### Set up
Build instructions have to be written in a file called `CMakeLists.txt`:
```cmake
# CMakeLists.txt:

cmake_minimum_required(VERSION 3.12)    # declare a minimum CMake version required to build the project (3.12)
project(TestProject VERSION 1.0.0)      # declare the project's name (TestProject), and current version (1.0.0)

add_executable(main main.c)             # add the source file (main.c) to the target (main),
                                        # multiple source files can be added
```
The sources' paths passed to `add_executable()`, `add_library()`, or `target_sources()`, are relative to the directory where the target is created.\
Run `cmake ..` from the build directory to configure CMake.

### Building
CMake allows building without knowing the build system underneath. You can run a build with `cmake --build [path to build]`.\
CMake generated build systems (e.g. `make` files) can detect if `CMakeLists.txt` has been updated and reconfigure accordingly.

## Adding a library

We'll create a library called `say-hello`, composed of a source file `say-hello.c` and header file `say-hello.h`; and we'll add it to `TestProject`:
```cmake
# CMakeLists.txt:

cmake_minimum_required(VERSION 3.12)
project(TestProject VERSION 1.0.0)

add_library(                                    # build a library (STATIC by default)
    say-hello                                   # called (say-hello)
    say-hello.h                                 # declared in (say-hello.h)
    say-hello.c                                 # defined in (say-hello.c)
)

add_executable(main main.c)

target_link_libraries(main PRIVATE say-hello)   # link library (say-hello) to target (main) in "PRIVATE" link interface mode 
```
With the `add_library()` command you can explicitly specify if you want to create a static library (`STATIC`) or a dynamic one (`SHARED`):
```cmake
add_library(
    say-hello
    SHARED  # say-hello will be built as a dynamic (shared) library
    # ...
)
```
You can also use `cmake -D BUILD_SHARED_LIBS=TRUE .` from the command line to reconfigure with the creation of dynamic libraries set as default.

There's a third option, `MODULE`, it's used for creating dynamically-linkable-only libraries (e.g. with `dlopen` on Linux, or `LoadLibrary` on Windows). They can be used to create optional plugins loaded runtime.

## Working with subdirectories and multiple sources

The previous project has been modified and made more complex:
```
.
├── CMakeLists.txt (1)
├── hello
│   ├── CMakeLists.txt (3)
│   └── main.c
└── say-hello
    ├── CMakeLists.txt (2)
    └── src
        └── say-hello
            ├── hello.c
            └── hello.h
```
The numbers between parenthesis have been added to distinguish the files more easily, so, `CMakeLists.txt (1)` was the original CMakeLists file.

First of all, `CMakeLists.txt (1)` needs to include the newly created subdirectories `hello` and `say-hello`:
```cmake
# CMakeLists.txt (1):

cmake_minimum_required(VERSION 3.12)
project(TestProject VERSION 1.0.0)

add_subdirectory(say-hello) # the path to the directory relative to this file
                            # here it will include `CMakeLists.txt (2)`
add_subdirectory(hello)     # the library must be defined before it's utilization
```
Next, we'll move the `add_library()` command in the `CMakeLists.txt (2)` file, and we'll fix the paths:
```cmake
# CMakeLists.txt (2):

add_library(
    say-hello
    src/say-hello/say-hello.h
    src/say-hello/say-hello.c
)
```
`CMakeLists.txt (3)` will contain instructions to build only the `main` target:
```cmake
# CMakeLists.txt (3):

add_executable(main main.c)
target_link_libraries(main PRIVATE say-hello)
```
At this state, the library `say-hello` will compile, but the program `main` will not. This is because `main.c` is still asking for `say-hello.h`, but the file has been moved away from `main.c`'s directory. We need to add a new include path so the compiler will be able to find the header file:
```cmake
# CMakeLists.txt (2):
# ...

target_include_directories(say-hello PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src")  # declare folder/s ("${CMAKE_CURRENT_SOURCE_DIR}/src") as include directories
                                                                                # for target (say-hello)
                                                                                # with `PUBLIC` visibility mode
```
With the `target_include_directories()` command you can specify to who the include directories will be added:
* `PUBLIC`: to the target itself and to who uses the library
* `PRIVATE`: to the target itself only
* `INTERFACE`: to who uses the library only

Remember to fix the header path in `main.c` as well, with the path relative to the newly added include directory:
```c
// main.c

#include <say-hello/hello.hpp>
```