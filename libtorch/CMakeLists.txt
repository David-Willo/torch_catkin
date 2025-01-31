cmake_minimum_required(VERSION 3.0)
project(libtorch)

find_package(catkin_simple REQUIRED)
catkin_simple()

if (CMAKE_SYSTEM_PROCESSOR MATCHES "(aarch64)|(ARM64)")
    set(TAR_FILE ${PROJECT_SOURCE_DIR}/libtorch-orin-jp50.zip)
else ()
    set(TAR_FILE ${PROJECT_SOURCE_DIR}/libtorch-cxx11-abi-shared-with-deps-1.13.1+cu117.zip)
endif ()
MESSAGE(WARNING "SYSTEM_ARCHITECTURE: ${CMAKE_SYSTEM_PROCESSOR}. USE TAR: ${TAR_FILE}")

execute_process(COMMAND 
${CMAKE_COMMAND} -E tar xvzf ${TAR_FILE})

set(BIN_DIR libtorch)

# message(STATUS ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

set(LIBTORCH_INCLUDE_DEST ${CATKIN_DEVEL_PREFIX}/include/libtorch/include)
file(MAKE_DIRECTORY ${LIBTORCH_INCLUDE_DEST})

file(GLOB LIBTORCH_HEADERS ${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/include/*)
file(GLOB LIBTORCH_LIBS ${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/lib/*.so*
${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/lib/*.a)
file(GLOB LIBTORCH_LIBS_MIN ${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/lib/*.so
${CMAKE_CURRENT_BINARY_DIR}/${BIN_DIR}/lib/*.a)

file(COPY ${LIBTORCH_HEADERS} DESTINATION ${LIBTORCH_INCLUDE_DEST})
file(COPY ${LIBTORCH_LIBS} DESTINATION ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_LIB_DESTINATION}/)



cs_install()

cs_export(INCLUDE_DIRS ${LIBTORCH_INCLUDE_DEST}
                       ${LIBTORCH_INCLUDE_DEST}/torch/csrc/api/include
          LIBRARIES torch torch_cpu c10 c10_cuda #kineto
                    # ${TORCH_EXPORT_LIBS} 
                    # ${CUDA_LIBRARIES}
                    # ${CUDA_CUDA_LIB}
                    # ${CUDA_NVRTC_LIB}
                    # ${LIBNVTOOLSEXT}
          DEPENDS CUDA)
# cs_export()
