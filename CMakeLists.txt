cmake_minimum_required(VERSION 3.12)
project(depth-anything-tensorrt)

# Set C++ standard
set(CMAKE_CXX_STANDARD 17)

# Add source files
set(SOURCES
	main.cpp
	utils.cpp
	depth_anything.cpp
)

# Add headers
set(HEADERS
	utils.h
	depth_anything.h
)

# Find and include CUDA
find_package(CUDA REQUIRED)
include_directories(${CUDA_INCLUDE_DIRS})

# Find and include OpenCV
set(OpenCV_DIR "$ENV{OpenCV_DIR}" CACHE PATH "Path to OpenCV build directory")
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

# Set TensorRT path if not set in environment variables
set(TENSORRT_DIR "$ENV{TENSORRT_DIR}" CACHE PATH "Path to TensorRT root directory")

# Include TensorRT
include_directories(${TENSORRT_DIR}/include)
link_directories(${TENSORRT_DIR}/lib)

# TensorRT 10+ library names
if (WIN32)
    set(TENSORRT_LIBS nvinfer_10 nvinfer_plugin_10 nvonnxparser_10)
else()
    set(TENSORRT_LIBS nvinfer nvinfer_plugin nvonnxparser)
endif()

# Add executable
add_executable(${PROJECT_NAME} ${SOURCES} ${HEADERS})

# Link libraries
target_link_libraries(${PROJECT_NAME}
    ${OpenCV_LIBS}
    ${CUDA_LIBRARIES}
    ${TENSORRT_LIBS}
)
