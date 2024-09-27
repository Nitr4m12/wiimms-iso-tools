cmake_minimum_required(VERSION 3.15)
project(wbfs VERSION 1.0.0 LANGUAGES C)

find_package(OpenSSL REQUIRED)

add_library(${PROJECT_NAME})

add_subdirectory(include)
add_subdirectory(src)

target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)
target_link_libraries(${PROJECT_NAME} PUBLIC dclib)
target_link_libraries(${PROJECT_NAME} PUBLIC OpenSSL::SSL)
