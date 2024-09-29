cmake_minimum_required(VERSION 3.15)
project(wbfs VERSION 1.0.0 LANGUAGES C)

add_library(${PROJECT_NAME} STATIC)
add_subdirectory(include)
add_subdirectory(src)

target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

# list(APPEND CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

#add_subdirectory(libs/dclib)
target_link_libraries(${PROJECT_NAME} PUBLIC dclib)

find_package(OpenSSL REQUIRED)
target_link_libraries(${PROJECT_NAME} PUBLIC OpenSSL::SSL)

# ===========================
#     COMPILE DEFINITIONS    
# ===========================

target_compile_definitions(${PROJECT_NAME} 
PUBLIC 
    LARGE_FILES
    _FILE_OFFSET_BITS=64
)

# =======================
#     COMPILE OPTIONS        
# =======================

target_compile_options(${PROJECT_NAME}
PUBLIC
    -fomit-frame-pointer
    -fno-strict-aliasing
    -Wall
    -Wno-parentheses
    -Wno-unused-function
    -O3
)

# =================
#       TESTS           
# =================
include(CTest)
set(BUILD_TESTING OFF)
if(BUILD_TESTING)
    add_subdirectory(tests)
endif(BUILD_TESTING)
