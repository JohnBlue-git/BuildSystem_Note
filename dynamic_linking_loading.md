
## Source code

To do dynamic linking, we create share library (.so).
\
To do dynamic loading, we use dlopen() to load the share library.

### Create Shared Library

my_shared_lib.c:
``` C
#include <stdio.h>

void hello() {
    printf("Hello from the shared library!\n");
}
```

compile this file into a shared library
```console
# -fPIC: This stands for "Position Independent Code". It generates machine code that can be loaded at any memory address.

# -shared: This option tells the compiler to create a shared library rather than a regular executable.

# The order of options does not affect the outcome

gcc -fPIC -shared -o libmy_shared_lib.so my_shared_lib.c
```

### Create Main Program

main.c
```C
#include <stdio.h>
#include <dlfcn.h>

int main() {
    void *handle;
    void (*hello)();
    char *error;

    // Load the shared library
    handle = dlopen("./libmy_shared_lib.so", RTLD_LAZY);
    if (!handle) {
        fprintf(stderr, "%s\n", dlerror());
        return 1;
    }

    // Clear any existing errors
    dlerror();

    // Get a pointer to the 'hello' function
    *(void **)(&hello) = dlsym(handle, "hello");

    // Check for any errors
    error = dlerror();
    if (error != NULL) {
        fprintf(stderr, "%s\n", error);
        dlclose(handle);
        return 1;
    }

    // Call the 'hello' function
    (*hello)();

    // Close the shared library
    dlclose(handle);

    return 0;
}
```

compile the Main Program
```console
# -ldl: This tells the linker to link against the libdl library. The libdl library provides functions for dynamic linking, such as dlopen, dlsym, and dlclose

gcc -o main main.c -ldl
```

## MakeFile

```makefile
# Compiler
CC = gcc

# Compiler flags
CFLAGS = -Wall -fPIC

# Target shared library
TARGET_LIB = libmy_shared_lib.so

# Source files
SRC = my_shared_lib.c
OBJ = $(SRC:.c=.o)

# Target executable
TARGET = main

# Build shared library
$(TARGET_LIB): $(OBJ)
    $(CC) -shared -o $@ $^

# Build object files
%.o: %.c
    $(CC) $(CFLAGS) -c $< -o $@

# Build main program
$(TARGET): main.o $(TARGET_LIB)
    $(CC) -o $@ main.o -ldl

# Compile main program object file
main.o: main.c
    $(CC) $(CFLAGS) -c main.c -o main.o

# Clean up
clean:
    rm -f $(TARGET) $(TARGET_LIB) *.o
```

## CMake

```cmake
cmake_minimum_required(VERSION 3.10)
project(ExampleProject)

# Set the C standard
set(CMAKE_C_STANDARD 99)

# Add the shared library
add_library(my_shared_lib SHARED
    my_shared_lib.c)

# Add the executable
add_executable(main
    main.c)

# Link the executable with the shared library and dynamic linker
target_link_libraries(main
    my_shared_lib
    dl)
```

## Meson

```meson
project('ExampleProject', 'c')

# Add the shared library
my_shared_lib = shared_library('my_shared_lib',
    'my_shared_lib.c')

# Add the executable
executable('main',
    'main.c',
    link_with : my_shared_lib,
    dependencies : [dependency('dl')])
```
