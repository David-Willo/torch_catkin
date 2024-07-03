cmake_minimum_required(VERSION 3.0)
project(torch_tensorrt)

find_package(catkin_simple REQUIRED)
catkin_simple(ALL_DEPS_REQUIRED)

# include(ExternalProject)

# ExternalProject_Add(torch_trt_src
#   URL http://gofile.me/72EEc/jzyfIHGkI
#   # URL_MD5 9e9ebe9c1fe98c468f6e53f5c3c49716
#   UPDATE_COMMAND ""
#   # SOURCE_DIR ${OPENCV_SRC_PATH}
#   # BINARY_DIR ${OPENCV_BUILD_PATH}
#   PATCH_COMMAND pwd
#   CONFIGURE_COMMAND pwd
#   BUILD_COMMAND pwd
#   INSTALL_COMMAND pwd
# )

find_library(NVINFER  NAMES nvinfer)
find_library(NVINFERPLUGIN NAMES nvinfer_plugin)
find_library(NVPARSERS NAMES nvparsersn)
find_library(NVONNXPARSER NAMES nvonnxparser)
find_library(NVONNXPARSERRUNTIME NAMES nvonnxparser_runtime)


if (CMAKE_SYSTEM_PROCESSOR MATCHES "(aarch64)|(ARM64)")
    set(TAR_FILE ${PROJECT_SOURCE_DIR}/libtorchtrt-orin-jp50.tar.gz)
else ()
    set(TAR_FILE ${PROJECT_SOURCE_DIR}/libtorchtrt-1.3.0-cudnn8.5-tensorrt8.5-cuda11.7-libtorch1.13.0-x86_64-linux.tar.gz)
endif ()
MESSAGE(WARNING "SYSTEM_ARCHITECTURE: ${CMAKE_SYSTEM_PROCESSOR}. USE TAR: ${TAR_FILE}")


execute_process(COMMAND 
${CMAKE_COMMAND} -E tar -xvzf ${TAR_FILE})

set(BIN_DIR torch_tensorrt)

message(STATUS ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

set(TORCHTRT_INCLUDE_DEST ${CATKIN_DEVEL_PREFIX}/include/torch_tensorrt/)
file(MAKE_DIRECTORY ${TORCHTRT_INCLUDE_DEST})

file(GLOB TORCHTRT_HEADERS ${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/include/torch_tensorrt/*)
file(GLOB TORCHTRT_LIBS ${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/lib/*.so)

file(COPY ${TORCHTRT_HEADERS} DESTINATION ${TORCHTRT_INCLUDE_DEST})
file(COPY ${TORCHTRT_LIBS} DESTINATION ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_LIB_DESTINATION}/)

cs_export(INCLUDE_DIRS ${TORCHTRT_INCLUDE_DEST}
          LIBRARIES torchtrt_plugins torchtrt_runtime torchtrt
          DEPENDS NVINFER NVINFERPLUGIN NVPARSERS NVONNXPARSER NVONNXPARSERRUNTIME)