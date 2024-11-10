
## About this readme

Some sample code yet organized ...

## Source code

CMakeLists.txt
```cmake
CMAKE_MINIMUM_REQUIRED(VERSION 3.11)
PROJECT(3rdPartyAPI)

IF (NOT DEFINED PROJECT_PATH) SET (PROJECT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/../..) ENDIF()
IF (NOT DEFINED PREFIX) FILE(STRINGS "${PROJECT_PATH}/prefix" PREFIX_PATH) SET (PREFIX ${PREFIX_PATH}) ENDIF()

SET (CMAKE_BUILD_TYPE "Debug")

OPTION(${PROJECT_NAME}_BOOST "Build boost library the projects it depends on." ON)
IF(${PROJECT_NAME}_BOOST) include("${CMAKE_CURRENT_SOURCE_DIR}/libBoostBuild.cmake")ENDIF()

OPTION(${PROJECT_NAME}_EVENT "Build libevent the projects it depends on." ON)
IF(${PROJECT_NAME}_EVENT) include("${CMAKE_CURRENT_SOURCE_DIR}/libEventBuild.cmake") ENDIF()

OPTION(${PROJECT_NAME}_ZMQ "Build libzmq the projects it depends on." ON)
IF(${PROJECT_NAME}_ZMQ) include("${CMAKE_CURRENT_SOURCE_DIR}/libZmqBuild.cmake") ENDIF()

OPTION(${PROJECT_NAME}_HIREDIS "Build libhiredis the projects it depends on." ON)
IF(${PROJECT_NAME}_HIREDIS) include("${CMAKE_CURRENT_SOURCE_DIR}/libHiredisBuild.cmake") ENDIF()

OPTION(${PROJECT_NAME}_MONGOC "Build libmongoc and libbson the projects it depends on." ON)
IF(${PROJECT_NAME}_MONGOC) include("${CMAKE_CURRENT_SOURCE_DIR}/libMongoCBuild.cmake") ENDIF()
```

libBoostBuild.cmake
```cmake
execute_process(COMMAND tar -zxvf ${PROJECT_PATH}/3rdPartyAPI/boost_1_58_0.tar.gz WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR})
execute_process(COMMAND ./bootstrap.sh --with-libraries=all --prefix=${PREFIX} WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/boost_1_58_0)
execute_process(COMMAND ./b2 install -j6 --buildtype=complete WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/boost_1_58_0)
```

libEventBuild.cmake
```cmake
include(ExternalProject)
ExternalProject_Add( libevent URL "${PROJECT_PATH}/3rdPartyAPI/libevent-2.1.11-stable.tar.gz"
    BUILD_IN_SOURCE 1
    CONFIGURE_COMMAND ./configure --prefix=${PREFIX}
    BUILD_COMMAND make
    INSTALL_COMMAND make install
)
```

libHiredisBuild.cmake
```cmake
include(ExternalProject)
ExternalProject_Add( libhiredis URL "${PROJECT_PATH}/3rdPartyAPI/hiredis-0.13.3.tar.gz"
	BUILD_IN_SOURCE 1
	CONFIGURE_COMMAND ""
	BUILD_COMMAND make
	INSTALL_COMMAND make PREFIX=${PREFIX} install
)
```

libZmqBuild.cmake
```cmake
include(ExternalProject)
ExternalProject_Add(libzmq URL "${PROJECT_PATH}/3rdPartyAPI/zeromq-4.1.5.tar.gz"
	BUILD_IN_SOURCE 1
	CONFIGURE_COMMAND ./configure --prefix=${PREFIX}
	BUILD_COMMAND make
	INSTALL_COMMAND make install
)
```

libMongoCBuild.cmake
```
include(ExternalProject)
#set(common_cmake_cache_args DCMAKE_CXX_COMPILER:PATH=${CMAKE_CXX_COMPILER})
#if(NOT DEFINED CMAKE_CONFIGURATION_TYPES)
	list(APPEND common_cmake_cache_args
	DCMAKE_BUILD_TYPE:STRING=${CMAKE_BUILD_TYPE}
)
#endif()
ExternalProject_Add( libmongoc URL "${PROJECT_PATH}/3rdPartyAPI/mongo-c-driver-1.23.2.tar.gz"
	CMAKE_CACHE_ARGS #${common_cmake_cache_args} -DCMAKE_INSTALL_PREFIX:PATH=${PREFIX} -DENABLE_TESTS:BOOL=OFF -DENABLE_STATIC:BOOL=OFF -DENABLE_EXAMPLES:BOOL=OFF -DENABLE_EXTRA_ALIGNMENT:BOOL=OFF -DENABLE_AUTOMATIC_INIT_AND_CLEANUP:BOOL=OFF
)
```
