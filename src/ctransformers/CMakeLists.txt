cmake_minimum_required(VERSION 3.17)
project(ctransformers C CXX)

# set(CT_INSTRUCTIONS "avx2" CACHE STRING "avx2 | avx | basic")

# option(CT_CUBLAS "Use cuBLAS" OFF)
# set(CT_CUDA_DMMV_X "32" CACHE STRING "x stride for dmmv CUDA kernels")
# set(CT_CUDA_DMMV_Y "1" CACHE STRING "y block size for dmmv CUDA kernels")
# set(CT_CUDA_KQUANTS_ITER "2" CACHE STRING "iters/thread per block for Q2_K/Q6_K")

# message(STATUS "CT_INSTRUCTIONS: ${CT_INSTRUCTIONS}")
# message(STATUS "CT_CUBLAS: ${CT_CUBLAS}")

set(BUILD_SHARED_LIBS ON)
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/$<0:>)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/$<0:>)

# Compile Flags

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)

# if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
#     set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
#     set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "RelWithDebInfo")
# endif()

if (NOT MSVC)
    set(c_flags
        -Wall
        -Wextra
        -Wpedantic
        -Wcast-qual
        -Wdouble-promotion
        -Wshadow
        -Wstrict-prototypes
        -Wpointer-arith
    )
    set(cxx_flags
        # TODO(marella): Add other warnings.
        # -Wall
        -Wextra
        -Wpedantic
        -Wcast-qual
        -Wno-unused-function
        -Wno-multichar
    )
endif()

add_compile_options(
    "$<$<COMPILE_LANGUAGE:C>:${c_flags}>"
    "$<$<COMPILE_LANGUAGE:CXX>:${cxx_flags}>"
)

# Architecture Flags

add_compile_options(-mcpu=neoverse-512tvb)

# Library

add_library(
    ctransformers SHARED
    models/llm.cc
    models/ggml/ggml.c
    models/ggml/k_quants.c
)

target_include_directories(ctransformers PRIVATE models)
target_link_libraries(ctransformers PRIVATE Threads::Threads)
set_target_properties(ctransformers PROPERTIES POSITION_INDEPENDENT_CODE ON)
target_compile_definitions(ctransformers PRIVATE GGML_USE_K_QUANTS)

# scikit-build

install(
    TARGETS ctransformers
    LIBRARY DESTINATION ctransformers/lib/local
    RUNTIME DESTINATION ctransformers/lib/local
)
