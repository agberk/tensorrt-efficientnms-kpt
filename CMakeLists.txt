cmake_minimum_required(VERSION 3.18)
project(EfficientNMS_KPT_Plugin LANGUAGES C CXX CUDA)

# Adjust or set this before running cmake
if(NOT DEFINED ENV{TENSORRT_DIR})
    message(FATAL_ERROR "Please set the TENSORRT_DIR environment variable to your TensorRT install path")
endif()

if(NOT DEFINED ENV{TENSORRT_REPO})
    message(FATAL_ERROR "Please set TENSORRT_REPO to the path of the cloned TensorRT repo (e.g., ~/TensorRT)")
endif()

set(TENSORRT_DIR $ENV{TENSORRT_DIR})

# Header paths
include_directories(${TENSORRT_DIR}/include/x86_64-linux-gnu/)
include_directories($ENV{TENSORRT_REPO}/plugin/)
include_directories(/usr/local/cuda/include)

# Link paths
link_directories(${TENSORRT_DIR}/lib/x86_64-linux-gnu/)

# Build the plugin
add_library(nvinfer_plugin_kpt SHARED
    efficientNMSPlugin.cpp
    efficientNMSPlugin.h
    efficientNMSInference.cu
    efficientNMSInference.cuh
    efficientNMSInference.h
    efficientNMSParameters.h
)

# Compiler and CUDA settings
set_target_properties(nvinfer_plugin_kpt PROPERTIES
    POSITION_INDEPENDENT_CODE ON
    CUDA_SEPARABLE_COMPILATION ON
)

# Link against TensorRT and CUDA runtime
target_link_libraries(nvinfer_plugin_kpt
    nvinfer
    nvonnxparser
    nvinfer_plugin
    cudart
)
