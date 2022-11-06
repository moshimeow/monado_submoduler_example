# Copyright 2022, Collabora, Ltd. SPDX-License-Identifier: BSL-1.0 Authors:
# Moses Turner <moses@collabora.com>

cmake_minimum_required(VERSION 3.10.2)
project(MonadoSubmodulerExample VERSION 0.1.0)

# We make it Debug here to get good backtraces and asserts. Then, after this, we
# set O3 so it's still fast.
set(CMAKE_BUILD_TYPE Debug)

set(CMAKE_C_FLAGS
    "${CMAKE_C_FLAGS} -pedantic -Wall -Wextra -Wno-unused-parameter -Werror=incompatible-pointer-types -g -march=native -O3 -fno-omit-frame-pointer"
)
set(CMAKE_CXX_FLAGS
    "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wno-unused-parameter -g -march=native -O3 -fno-omit-frame-pointer"
)
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,--no-undefined")
set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_MODULE_LINKER_FLAGS} -Wl,--no-undefined")

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Default to PIC code
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Create copmile_commands.json so that Intellisense works
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Get Monado's CMake modules
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/monado/cmake")
list(APPEND CMAKE_MODULE_PATH
     "${CMAKE_CURRENT_SOURCE_DIR}/monado/cmake/sanitizers")

find_package(ONNXRuntime MODULE REQUIRED)

find_package(Eigen3 3 REQUIRED)
find_package(
  OpenCV REQUIRED
  COMPONENTS core
             calib3d
             highgui
             imgproc
             imgcodecs
             features2d
             video
             CONFIG)

# Make sure we have pretty colors

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
  add_compile_options(-fdiagnostics-color=always)
elseif("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  add_compile_options(-fcolor-diagnostics)
endif()

set(MONADO_SRC_XRT ${CMAKE_CURRENT_SOURCE_DIR}/monado/src/xrt)

# Set configuration options for Monado
set(XRT_FEATURE_COMPOSITOR_MAIN OFF) # we are not using this
set(XRT_FEATURE_COMPOSITOR_NULL OFF) # we are not using this
set(XRT_FEATURE_OPENXR OFF) # we aren't using this
set(XRT_FEATURE_IPC OFF)
set(XRT_FEATURE_OPENXR OFF)

set(XRT_FEATURE_RENDERDOC OFF)
set(XRT_FEATURE_SERVICE OFF)
set(XRT_FEATURE_SERVICE_SYSTEMD OFF)
set(XRT_FEATURE_STEAMVR_PLUGIN OFF) # for now. we'll see what transpires
set(XRT_FEATURE_TRACING OFF) # ditto
set(XRT_FEATURE_SLAM OFF) # Not using it
set(XRT_HAVE_BASALT_SLAM OFF)

set(XRT_HAVE_OPENGL OFF)
set(XRT_HAVE_VULKAN OFF)

set(XRT_FEATURE_CLIENT_DEBUG_GUI OFF)

set(XRT_HAVE_SDL2 OFF) # Don't need this - for debug UI

# No drivers!
set(XRT_BUILD_DRIVER_ANDROID OFF)
set(XRT_BUILD_DRIVER_ARDUINO OFF)
set(XRT_BUILD_DRIVER_DAYDREAM OFF)
set(XRT_BUILD_DRIVER_DEPTHAI OFF)
set(XRT_BUILD_DRIVER_EUROC OFF)
set(XRT_BUILD_DRIVER_HANDTRACKING OFF)
set(XRT_BUILD_DRIVER_TWRAP OFF)
set(XRT_BUILD_DRIVER_HDK OFF)
set(XRT_BUILD_DRIVER_HYDRA OFF)
set(XRT_BUILD_DRIVER_ILLIXR OFF)
set(XRT_BUILD_DRIVER_NS OFF)
set(XRT_BUILD_DRIVER_OHMD OFF)
set(XRT_BUILD_DRIVER_OPENGLOVES OFF)
set(XRT_BUILD_DRIVER_PSMV OFF)
set(XRT_BUILD_DRIVER_PSVR OFF)
set(XRT_BUILD_DRIVER_QWERTY OFF)
set(XRT_BUILD_DRIVER_REALSENSE OFF)
set(XRT_BUILD_DRIVER_REMOTE OFF)
set(XRT_BUILD_DRIVER_RIFT_S OFF)
set(XRT_BUILD_DRIVER_SURVIVE OFF)
set(XRT_BUILD_DRIVER_ULV2 OFF)
set(XRT_BUILD_DRIVER_VF OFF)
set(XRT_BUILD_DRIVER_VIVE OFF)
set(XRT_BUILD_DRIVER_WMR OFF)
set(XRT_BUILD_DRIVER_SIMULAVR OFF)

# There is no clean way to disable the prober/builders (as far as I can see?)
# So we enable one driver so that the legacy builder does not die
set(XRT_BUILD_DRIVER_SIMULATED ON)

set(XRT_BUILD_SAMPLES OFF)

set(BUILD_TESTING OFF)
set(BUILD_DOC OFF)



set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


# Recurse into the main Monado distribution
add_subdirectory(monado)

add_executable(test src/main.cpp)

target_link_libraries(test PRIVATE 
${OpenCV_LIBRARIES}
 aux_math 
 # StereoKitC # uncomment if u want stereokit
 aux_util)

target_include_directories(test PRIVATE ${EIGEN3_INCLUDE_DIR})
