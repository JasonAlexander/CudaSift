cmake_minimum_required(VERSION 2.8)
project(cudasift)

# find_package(OpenCV REQUIRED)
find_package(CUDA REQUIRED)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR})
include(CudaComputeTargetFlags.cmake)
append_target_arch_flags()

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-O3 -msse2 -msse3 -Wall -std=c++11")
set(CUDA_ARCH_BIN "20 30 32 35 37 50 52 61" CACHE STRING "Specify 'real' GPU arch to build binaries for, BIN(PTX) format is supported. Example: 1.3 2.1(1.3) or 13 21(13)")
set(CUDA_ARCH_PTX "" CACHE STRING "Specify 'virtual' PTX arch to build PTX intermediate code for. Example: 1.0 1.2 or 10 12")

list(APPEND CUDA_NVCC_FLAGS "-O3;-Xcompiler;-fPIC")

include_directories(include)
include_directories(${CUDA_INCLUDE_DIRS})
# include_directories(${OpenCV_INCLUDE_DIRS})

file(GLOB_RECURSE cuda_sources src/*.cu)
set(cpp_sources src/sift_engine.cpp)
# set(cpp_sources src/mainSift.cpp src/geomFuncs.cpp)

# set(CUDA_SEPARABLE_COMPILATION ON)

cuda_compile(cuda_objects ${cuda_sources})
# cuda_add_executable(cudasift ${cuda_sources} ${cpp_sources})
# add_executable(cudasift ${cuda_sources} ${cuda_objects} ${cpp_sources})
add_library(cudasift SHARED ${cuda_sources} ${cuda_objects} ${cpp_sources})
target_link_libraries(cudasift ${CUDA_LIBRARIES})
# target_link_libraries(cudasift ${CUDA_LIBRARIES} ${OpenCV_LIBS})

# set_target_properties(cudasift PROPERTIES LINKER_LANGUAGE CXX)
