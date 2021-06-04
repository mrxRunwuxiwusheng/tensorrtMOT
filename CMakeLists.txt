cmake_minimum_required(VERSION 3.1) 
project(yolo-trt VERSION 1.0)            
enable_language(CUDA)

set(CMAKE_CXX_COMPILIER "/usr/bin/g++")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -O3 -Wno-write-strings")
set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-rpath -Wl,$ORIGIN")

#cuda
#export PATH=/usr/local/cuda-11.0/bin:$PATH
#include_directories(/usr/local/cuda/include)
#link_directories(/usr/local/cuda/lib64)
find_package(CUDA REQUIRED)
get_filename_component(CUDA_LIBRARY_DIR ${CUDA_CUDART_LIBRARY} DIRECTORY)
set(CMAKE_EXE_LINKER_FLAGS ${CMAKE_EXE_LINKER_FLAGS} "-L${CUDA_LIBRARY_DIR}")

#opencv
#set(OpenCV_DIR /opt/opencv-4.1.2/share/OpenCV)
find_package(OpenCV REQUIRED)
#SET(OpenCV_DIR /usr/local/include/opencv4)


find_package (Eigen3  REQUIRED NO_MODULE)
find_package(TensorflowCC REQUIRED)

#detector lib (cuda)
file(GLOB_RECURSE sources  "deep_sort/*/*.cpp" "deep_sort/*/*.h"    modules/*.hpp modules/*.cpp modules/*.h modules/*.cu extra/*.h)
#file(GLOB source_files "*.h" "*.cpp" "deep_sort/*/*.cpp" "deep_sort/*/*.h" )
add_library(detector SHARED ${sources})

target_include_directories(detector PRIVATE extra/ modules/   ${OpenCV_INCLUDE_DIRS} ${CUDA_TOOLKIT_ROOT_DIR}/include)
target_link_libraries(detector TensorflowCC::TensorflowCC Eigen3::Eigen nvinfer nvinfer_plugin nvparsers "stdc++fs")


add_executable(yolo-trt sample_detector.cpp )				  
target_include_directories(yolo-trt PRIVATE modules/ extra/ deep_sort/feature/ deep_sort/matching/ deep_sort/thirdPart)
target_link_libraries(yolo-trt detector ${OpenCV_LIBS} )

find_package (Eigen3 3.3 REQUIRED NO_MODULE)

include_directories(${EIGEN_INCLUDE_DIR})