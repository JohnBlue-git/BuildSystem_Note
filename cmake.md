## CMake Variables
- Pre-defined variable
- Normal variable
- Cache variable

### Common pre-defined variables

- CMAKE_BUILD_TYPE: \
Specifies the build configuration (e.g., Debug, Release).

- *_SOURCE_DIR and *_BINARY_DIR: \
Source directory means the root of the project and it is where CMakeLists.txt located; Binary directory is where we want the project to build code and generate binaries.
    * PROJECT_SOURCE_DIR & PROJECT_BINARY_DIR: \
    It will change as respect to current project()
    * CMAKE_CURRENT_SOURCE_DIR & CMAKE_CURRENT_BINARY_DIR: \
    Path to the current source or build directory.
```console
# We can use -S to set source directory
#        use -B to set binary directory
# (New in cmake version 3.24) To clear binary derectory (or cmake cache) before re-build, we can uuse --fresh
cmake -S <source-dir> -B <binary-dir> --fresh

# ref:
# https://cmake.org/cmake/help/latest/manual/cmake.1.html
```

- CMAKE_CXX_COMPILER: \
Path to the C++ compiler.

- CMAKE_C_COMPILER: \
Path to the C compiler.

- CMAKE_SYSTEM_NAME: \
Name of the operating system (e.g., Linux, Windows).

- CMAKE_SYSTEM_PROCESSOR: \
Processor architecture (e.g., x86_64).

### CMake variable properties

- Normal variable:
    * Type: \
    The type of the variable, typically STRING, BOOL, or PATH. 
    * Scope: \
    They have local scope within the directory and its subdirectories. They do not persist across CMake runs.
    * Non-persistency: \
    These variables are temporary and do not affect future CMake configurations. They are reset each time CMake is runand will be overwritten by sud-directory's set().
```cmake
set(SELECT_APP "APP1")
```

- Cache variable:
    * Type: \
    The type of the variable, typically STRING, BOOL, or PATH. 
    * Scope: \
    They have local scope within the directory and its subdirectories.
    * Persistency: \
    These variables' value will be remember accross the project and affect future CMake configurations (if CMake cache exist). The values set by user defined or under the top-level directory would overshadow the valuse set under the sud-directories. 
```cmake
# How to set cache variable via user input
# cmake -D <name>:<type>=<value> ..

# How to set cache variable within CMakeLists.txt
set(CACHE_SELECT_APP "APP1" CACHE STRING "")

# Priority:
# user input > top-level set() > sub-level set()
```


## CMake options

Provide a boolean option that the user can optionally select
```cmake
# How to set options
# cmake -DBUILD_APP1=OFF ..

# Define options
option(BUILD_APP1 "Build APP1" OFF)

# Include examples if the option is enabled
if(BUILD_APP1)
    add_subdirectory(APP1)
endif()
```

## CMake targets

### Add sub directory (sub-project)
```console
add_subdirectory(sub_directory)
```

### Include header
```console
# for all targets
# should place before all targets
include_directories()

# for specific target
# should place after target
target_include_directories()
```

### Add executable or library
```console
# exe
add_executable(app app.cpp)

# lib
add_library(lib STATIC lib.cpp)
```

### Link or Load library for a program 
C / C++ code will often link multiple libraryies to build application.
Static linkage is the basic level to link among libraries (.a file).
Dynamic linkage is a way link shared library (.so file). (app can share library to save memory usage)
Dynamic loaded is another way to only load the library if it's necessary (via using ldopen) \
Ref: https://makori-mildred.medium.com/how-to-create-a-dynamic-library-in-c-and-how-to-use-it-30c304f399a4
```console
# for all targets
# shoullld place before all targets
link_library(...)

# for specific target
# should place after target
target_link_library(<target> ...)
```
We don't need to defined whether it is static link or shared link.
Other feature:
- PUBLIC - This property is for me and everyone who depends on me is going to get this property.
- PRIVATE - This property is just for me. Whoever depends on me is not going to get this.
- INTERFACE - I do not need this for myself. But anyone who depends on me will get this property. \
Ref: https://www.youtube.com/watch?v=ARZd-fSUJXY

### Add Dependencies
```console
# lib
add_library(lib1 STATIC lib1.cpp)
add_library(lib2 STATIC lib2.cpp)

# lib2 depend on lib1
add_dependencies(lib2 lib1)
```


## Installation settings

The default installation prefix would usually be "/usr/local"; the executables and dlls would be placed in "/usr/local/bin"; and the header files' folder would be placed in "/usr/local/include" \
and here are the common modfier for installation: \
- **ARCHIVE**: static libraries .a \
- **LIBRARY**: shared libraries .so \
- **RUNTIME**: executables and dlls
```console
install(TARGETS <program>
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin # For Windows DLLs
)

install(DIRECTORY include/ DESTINATION include)
```
We can also set customized install prefix
```console
set(CMAKE_INSTALL_PREFIX "/desired/install/path" CACHE PATH "Installation Directory" FORCE)
install(...)
```
We can also set permission level for the installations
```console
install(TARGETS <program>
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
    PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE
                GROUP_READ GROUP_EXECUTE
                WORLD_READ WORLD_EXECUTE
)

install(FILES file.conf
    DESTINATION ${doc}
    PERMISSIONS OWNER_READ OWNER_WRITE
                GROUP_READ
                WORLD_READ
)
```
