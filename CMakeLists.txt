# Note: This is meant to be built in autoware docker images

# TODO: Set up a autoware free environment

cmake_minimum_required(VERSION 3.2)
project(cuda_playgound)

# set flags for CUDA availability
option(CUDA_AVAIL "CUDA available" OFF)
find_package(CUDA)
if(CUDA_FOUND)
  find_library(CUBLAS_LIBRARIES cublas HINTS
    ${CUDA_TOOLKIT_ROOT_DIR}/lib64
    ${CUDA_TOOLKIT_ROOT_DIR}/lib
  )
  if(CUDA_VERBOSE)
    message("CUDA is available!")
    message("CUDA Libs: ${CUDA_LIBRARIES}")
    message("CUDA Headers: ${CUDA_INCLUDE_DIRS}")
  endif()
  # Note: cublas_device was depreciated in CUDA version 9.2
  #       https://forums.developer.nvidia.com/t/where-can-i-find-libcublas-device-so-or-libcublas-device-a/67251/4
  #       In LibTorch, CUDA_cublas_device_LIBRARY is used.
  unset(CUDA_cublas_device_LIBRARY CACHE)
  set(CUDA_AVAIL ON)
else()
  message("CUDA NOT FOUND")
  set(CUDA_AVAIL OFF)
endif()

# # set flags for TensorRT availability
# option(TRT_AVAIL "TensorRT available" OFF)
# # try to find the tensorRT modules
# find_library(NVINFER nvinfer)
# find_library(NVONNXPARSER nvonnxparser)
# if(NVINFER AND NVONNXPARSER)
#   if(CUDA_VERBOSE)
#     message("TensorRT is available!")
#     message("NVINFER: ${NVINFER}")
#     message("NVONNXPARSER: ${NVONNXPARSER}")
#   endif()
#   set(TRT_AVAIL ON)
# else()
#   message("TensorRT is NOT Available")
#   set(TRT_AVAIL OFF)
# endif()

# # set flags for CUDNN availability
# option(CUDNN_AVAIL "CUDNN available" OFF)
# # try to find the CUDNN module
# find_library(CUDNN_LIBRARY
# NAMES libcudnn.so${__cudnn_ver_suffix} libcudnn${__cudnn_ver_suffix}.dylib ${__cudnn_lib_win_name}
# PATHS $ENV{LD_LIBRARY_PATH} ${__libpath_cudart} ${CUDNN_ROOT_DIR} ${PC_CUDNN_LIBRARY_DIRS} ${CMAKE_INSTALL_PREFIX}
# PATH_SUFFIXES lib lib64 bin
# DOC "CUDNN library."
# )
# if(CUDNN_LIBRARY)
#   if(CUDA_VERBOSE)
#     message(STATUS "CUDNN is available!")
#     message(STATUS "CUDNN_LIBRARY: ${CUDNN_LIBRARY}")
#   endif()
#   set(CUDNN_AVAIL ON)
# else()
#   message("CUDNN is NOT Available")
#   set(CUDNN_AVAIL OFF)
# endif()

# if(TRT_AVAIL AND CUDA_AVAIL AND CUDNN_AVAIL)
if(CUDA_AVAIL)
  include_directories(
    include
    ${CUDA_INCLUDE_DIRS}
  )
  # cuda_add_library(vector_add SHARED
  #   src/vector_add.cu
  # )
  cuda_add_executable(vector_add
    src/vector_add.cu
  )
  target_include_directories(vector_add
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
  )

  target_include_directories(vector_add
  SYSTEM PUBLIC
    ${CUDA_INCLUDE_DIRS}
  )

endif()