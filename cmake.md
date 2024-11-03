## Common feature / syntax of CMake

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

### Installation settings
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
