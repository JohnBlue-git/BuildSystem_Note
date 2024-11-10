
## Meson

folder structure
```console
```console
├── build
├── include
├── src
└── CMakeLists.txt
```

command to build
```console
# generate ...
meson build -D<define variable>=<value>

# then ninja build
cd build
ninja
ninja install
# ninja test and install
ninja test
sudo ninja install

# another way to trigger ninja
meson compile -C build
meson test -C build
sudo meson install -C build

# to setup and install dependency before build
meson setup build

# force to refresh
meson setup --reconfigure build
```

## Meson variable

### project() with compile options
```meson
project('my-sdbusplus', 'cpp', 'c',
    default_options: [
      'buildtype=debugoptimized',
      'cpp_std=c++23',
      'warning_level=3',
      'werror=true',
    ],
    version: '1.0.0',
    meson_version: '>=1.1.1',
)
```

### option
meson.options
```meson
option('asio-example', type: 'feature', description: 'Build asio-example', value : 'enabled')
```
meson.build
```meson
if not get_option('asio-example').disabled()
  subdir('asio-example')
endif
```
command
```console
# to enable
meson build -Dasio-example=enabled
# to disable
meson build -Dasio-example=disabled
```

## Meson dependency

### find program
```meson
python = import('python')
python_bin = python.find_installation('python3', modules:['inflection', 'yaml', 'mako'])

if not python_bin.found()
  error('No valid python3 installation found')
endif
```

### find library
```meson
libsystemd_pkg = dependency('libsystemd')
```

### declare dependency
```meson
boost_compile_args = [
    '-DBOOST_ASIO_DISABLE_THREADS',
    '-DBOOST_ALL_NO_LIB',
    '-DBOOST_SYSTEM_NO_DEPRECATED',
    '-DBOOST_ERROR_CODE_HEADER_ONLY',
    '-DBOOST_COROUTINES_NO_DEPRECATION_WARNING',
]

boost_dep = declare_dependency(
    dependencies: dependency('boost', required: false),
    compile_args: boost_compile_args)
```

### sub-project
subprojects/googletest.wrap
```meson
[wrap-git]
url = https://github.com/google/googletest
revision = HEAD
```
The following script will find the suub-project and build the depended components
```meson
cmake = import('cmake')
gtest_proj = cmake.subproject('googletest', required: true)
```

## Meson targets

### Add subdirectory (sub-project)
```meson
subdir('sub_directory')
```

###Include header
```meson
# Include directory for all targets
inc = include_directories('include_directory')

# Usage in target
executable('app', 'app.cpp', include_directories : inc)
```

### Add executable or library
```meson
# Executable
executable('app', 'app.cpp')

# Static Library
static_library('lib', 'lib.cpp')

# Shared Library
shared_library('libshared', 'libshared.cpp')
```

### Link or Load library
In Meson, the distinction between static and shared libraries is made during the target creation.
```meson
# Link libraries to executable or library
executable('app', 'app.cpp', link_with : 'lib')
```

### Add Dependencies
```meson
# Declare dependencies
dep_lib1 = static_library('lib1', 'lib1.cpp')
dep_lib2 = static_library('lib2', 'lib2.cpp')

# Add dependencies
exe = executable('app', 'app.cpp', link_with : [dep_lib1, dep_lib2])
```


## Installation settings

The default installation prefix in Meson is /usr/local. The installation paths for executables and libraries can be customized.
```meson
# Install files
install_data('file.conf', install_dir : join_paths(get_option('datadir'), 'doc'))

# Install header files
install_headers('header.h', subdir : 'include')

# Example for installing executables and libraries
executable('app', 'app.cpp', install : true)

static_library('lib', 'lib.cpp', install : true)

shared_library('libshared', 'libshared.cpp', install : true)
```

And if you need customized install prefix:
```meson
# Set install prefix
meson setup builddir --prefix /desired/install/path

meson install -C builddir
```

However, **Meson** doesn't set install prefixes directly within its script. It’s more of a command-line setup kind of deal when you configure your build directory.

**Meson** also can’t specify custom permissions during the install process like CMake does.
