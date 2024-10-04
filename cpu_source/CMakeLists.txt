cmake_minimum_required(VERSION 3.20)

project(BM3DCPU VERSION 2.6 LANGUAGES CXX)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Ofast -funroll-loops -fopenmp-simd")

add_library(bm3dcpu SHARED source.cpp)
target_include_directories(bm3dcpu PRIVATE ${VAPOURSYNTH_INCLUDE_DIRECTORY})
set_target_properties(bm3dcpu PROPERTIES 
    CXX_EXTENSIONS OFF
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON)

if(CMAKE_SYSTEM_PROCESSOR MATCHES "x86_64|AMD64") # Check for 64-bit x86
    if ((CMAKE_CXX_COMPILER_ID STREQUAL "GNU") OR (CMAKE_CXX_COMPILER_ID MATCHES "^(Apple)?Clang$"))
        target_compile_options(bm3dcpu PRIVATE "-mavx2;-mfma;-mpopcnt")
    elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
        target_compile_options(bm3dcpu PRIVATE "/arch:AVX2")
    endif()
endif()

install(TARGETS bm3dcpu
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})
