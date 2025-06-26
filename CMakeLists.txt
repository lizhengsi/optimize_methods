cmake_minimum_required(VERSION 3.16)
project(optimize_methods)

set(DEFAULT_BUILD_TYPE "Release")
# set(DEFAULT_BUILD_TYPE "Debug")

set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD 17)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread -fexceptions -g -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 ${CMAKE_CXX_FLAGS}")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS}")
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if (NOT DEFINED CMAKE_SUPPRESS_DEVELOPER_WARNINGS)
    set(CMAKE_SUPPRESS_DEVELOPER_WARNINGS 1 CACHE INTERNAL "No dev warnings")
endif ()

# lib path
set(Ceres_DIR ~/lib/ceres-solver-2.2.0/lib/cmake/Ceres)
set(G2O_ROOT ~/lib/g2o)
list(APPEND CMAKE_MODULE_PATH ~/lib/g2o/lib/cmake/g2o)
set(GTSAM_DIR ~/lib/gtsam-4.2/lib/cmake/GTSAM)

# Eigen
find_package(Eigen3 REQUIRED QUIET)
message(STATUS "EIGEN3_VERSION: ${EIGEN3_VERSION_STRING}")

# Ceres
find_package(Ceres REQUIRED QUIET)
message(STATUS "CERES_VERSION: ${CERES_VERSION}")

# g2o
find_package(G2O REQUIRED QUIET)
include_directories(${G2O_INCLUDE_DIR})
message(STATUS "G2O_INCLUDE_DIR: ${G2O_INCLUDE_DIR}")
message(STATUS "G2O_CORE_LIBRARY: ${G2O_CORE_LIBRARY}")
message(STATUS "G2O_FOUND: ${G2O_FOUND}")

#opencv
find_package(OpenCV REQUIRED)
message(STATUS "OpenCV_VERSION: ${OpenCV_VERSION}")

#gtsam
set(GTSAM_USE_SYSTEM_EIGEN ON)
find_package(GTSAM REQUIRED QUIET)
message(STATUS "GTSAM_VERSION: ${GTSAM_VERSION}")

include_directories(${EIGEN3_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
        ${CERES_INCLUDE_DIRS}
        ${G2O_INCLUDE_DIRS}
        ${GTSAM_INCLUDE_DIR}
        ${OpenCV_INCLUDE_DIRS})

link_directories(
        ~/lib/gtsam-4.2/lib
)

set(TARGET_NAME CeresCurveFitting)
add_executable(${TARGET_NAME} src/ceres_curve_fitting.cpp)
target_link_libraries(${TARGET_NAME} ${OpenCV_LIBS} ${CERES_LIBRARIES})

set(TARGET_NAME G2oCurveFitting)
add_executable(${TARGET_NAME} src/g2o_curve_fitting.cpp)
target_link_libraries(${TARGET_NAME} ${OpenCV_LIBS} ${G2O_CORE_LIBRARY} ${G2O_STUFF_LIBRARY} ${G2O_SOLVER_DENSE} fmt)

set(TARGET_NAME GtsamCurveFitting)
add_executable(${TARGET_NAME} src/gtsam_curve_fitting.cpp)
target_link_libraries(${TARGET_NAME} ${OpenCV_LIBS} gtsam metis-gtsam)
