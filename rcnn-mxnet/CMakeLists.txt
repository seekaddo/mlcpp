cmake_minimum_required(VERSION 3.10)
project(rcnn)

set(LIBRARIES_DIR "" CACHE PATH "path to mxnet install dir")

set(CMAKE_VERBOSE_MAKEFILE ON)

set(CMAKE_CXX_FLAGS "-std=c++14 -fopenmp -pthread -msse3 -Wall -Wextra -Wno-int-in-bool-context -Wno-unused-parameter -Wno-unknown-pragmas -Wno-sign-compare")


set(CUDA_NVCC_FLAGS "-std=c++14;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wall;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wextra;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wno-unused-parameter;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wno-unknown-pragmas;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wno-int-in-bool-context;")
list(APPEND CUDA_NVCC_FLAGS "--compiler-options;-Wno-sign-compare;")
list(APPEND CUDA_NVCC_FLAGS "--expt-relaxed-constexpr;")

set(CMAKE_CXX_FLAGS_RELEASE "-O3 -DNDEBUG")
set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")

set(CUDA_NVCC_FLAGS_RELEASE "-O3;-DNDEBUG;")
set(CUDA_NVCC_FLAGS_DEBUG "-g;-O0;")
set(CUDA_PROPAGATE_HOST_FLAGS ON)

message(STATUS "mxnet install path: ${LIBRARIES_DIR}")

include_directories(${CMAKE_SOURCE_DIR}/../third_party/eigen)
include_directories(${CMAKE_SOURCE_DIR}/../third_party/json/include)

include_directories(${LIBRARIES_DIR}/include)
include_directories(${LIBRARIES_DIR}/include/mxnet)
include_directories(${LIBRARIES_DIR}/include/dcml)

include_directories(${LIBRARIES_DIR}/sources/incubator-mxnet/3rdparty/dlpack/include/)
include_directories(${LIBRARIES_DIR}/sources/incubator-mxnet/src/operator/)
include_directories(${LIBRARIES_DIR}/sources/incubator-mxnet/mshadow)
include_directories(${LIBRARIES_DIR}/sources/incubator-mxnet/.build/mklml/mklml_lnx_2018.0.3.20180406/include/)

link_directories(${LIBRARIES_DIR}/lib)
link_directories(${LIBRARIES_DIR}/lib64)

find_package(OpenCV REQUIRED)
find_package(BLAS REQUIRED)
find_package(CUDA REQUIRED)

include_directories(${CUDA_INCLUDE_DIRS})

set(requiredlibs "stdc++fs")
list(APPEND requiredlibs "stdc++")
list(APPEND requiredlibs "ncurses")

set(SOURCES_COMMON
    params.h
    params.cpp
    imageutils.h
    imageutils.cpp
    resnet.h
    resnet.cpp
    rcnn.h
    rcnn.cpp
    bbox.h
    bbox.cpp
    coco.h
    coco.cpp
    imagedb.h
    imagedb.cpp
    mxutils.h
    mxutils.cpp)


set(SOURCES_TRAIN
    rcnn_train.cpp   
    anchorgenerator.h
    anchorgenerator.cpp
    anchorsampler.h
    anchorsampler.cpp
    trainiter.h
    trainiter.cpp
    gputrainiter.h
    gputrainiter.cpp
    README.md
    get_model.sh
    proposaltarget_op.h
    proposaltarget_op.hpp
    proposaltarget_op.cu
    proposaltarget_op.cpp
    metrics.h
    metrics.cpp
    reporter.h
    reporter.cpp
    )

set(SOURCES_DEMO
    rcnn_demo.cpp    
    )

##add_executable(rcnn_train ${SOURCES_TRAIN} ${SOURCES_COMMON})
cuda_add_executable(rcnn_train ${SOURCES_TRAIN} ${SOURCES_COMMON})
target_link_libraries(rcnn_train ${requiredlibs})
target_link_libraries(rcnn_train ${BLAS_LIBRARIES} ${OpenCV_LIBS} ${CUDA_LIBRARIES})
target_link_libraries(rcnn_train optimized mxnet debug mxnetd)
target_link_libraries(rcnn_train optimized mkldnn debug mkldnnd)


#add_executable(rcnn_demo ${SOURCES_DEMO} ${SOURCES_COMMON})
cuda_add_executable(rcnn_demo ${SOURCES_DEMO} ${SOURCES_COMMON})
target_link_libraries(rcnn_demo ${requiredlibs})
target_link_libraries(rcnn_demo ${BLAS_LIBRARIES} ${OpenCV_LIBS} ${CUDA_LIBRARIES})
target_link_libraries(rcnn_demo optimized mxnet debug mxnetd)
target_link_libraries(rcnn_demo optimized mkldnn debug mkldnnd)

#add_executable(load_eval load_eval_ex.cpp imageutils.cpp)
#target_link_libraries(load_eval ${requiredlibs})
#target_link_libraries(load_eval ${BLAS_LIBRARIES} ${OpenCV_LIBS})
#target_link_libraries(load_eval optimized mxnet debug mxnetd)
#target_link_libraries(load_eval optimized mkldnn debug mkldnnd)



