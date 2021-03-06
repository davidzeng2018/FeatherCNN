cmake_minimum_required(VERSION 2.8.10)

file(GLOB LIB_HEADERS ./include/booster/*.h)

set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -std=c++11 -Wno-format")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -g -std=c++11 -Wall")

include_directories("./include/")

#if(FEATHER_ARM)
#	message(STATUS "Using ARM Neon accelerated backend.")
#	add_subdirectory(./arm)
#	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -DFEATHER_ARM")
#	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DFEATHER_ARM")
#	add_library(feather STATIC ${LIB_SRC} ${LIB_HEADERS} ${LAYER_SRC} ${LAYER_HEADERS} $<TARGET_OBJECTS:arm_backend_obj>)
#elseif(FEATHER_AVX)
#	list(REMOVE_ITEM LAYER_HEADERS "layers/conv_winograd_layer.h")
#	message(STATUS "Using AVX accelerated backend.")
#	set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -DFEATHER_AVX")
#	set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DFEATHER_AVX")
#	add_subdirectory(./avx)
#	add_library(feather STATIC ${LIB_SRC} ${LIB_HEADERS} ${LAYER_SRC} ${LAYER_HEADERS} $<TARGET_OBJECTS:avx_backend_obj>)
#else()
#	message(STATUS "Using dummy backend.")
#	add_subdirectory(./general)
#	add_library(feather STATIC ${LIB_SRC} ${LIB_HEADERS} ${LAYER_SRC} ${LAYER_HEADERS} $<TARGET_OBJECTS:general_backend_obj>)
#endif()

if(BOOSTER_AVX)
message(STATUS "Compiling booster AVX version.")
add_subdirectory(./avx)
add_library(booster STATIC $<TARGET_OBJECTS:booster_avx_obj>)
elseif(BOOSTER_ARM)
add_subdirectory(./arm)
add_library(booster STATIC $<TARGET_OBJECTS:booster_arm_obj>)
endif()

set(BOOSTER_INSTALL_DIR "${PROJECT_BINARY_DIR}/install/booster")

message(Library headers: ${LIB_HEADERS})
install(TARGETS booster DESTINATION "${BOOSTER_INSTALL_DIR}/lib")
install(FILES ${LIB_HEADERS} DESTINATION "${BOOSTER_INSTALL_DIR}/include/booster")
