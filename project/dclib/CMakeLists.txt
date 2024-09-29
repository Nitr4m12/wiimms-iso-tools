cmake_minimum_required(VERSION 3.5)
project(dclib VERSION 1.0.0 LANGUAGES C)

add_library(${PROJECT_NAME})
target_include_directories(${PROJECT_NAME}
    PUBLIC
        $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/src
)

add_subdirectory(src)

## ----------------------
##    PREPARE VARIABLES
## ----------------------

if (NOT DEFINED DCLIB_THREAD)
    set(DCLIB_THREAD 0)    
endif()

target_link_libraries(${PROJECT_NAME} PUBLIC mariadb)
if (NOT mariadb_FOUND)
    execute_process(COMMAND "[[ -f /usr/include/mysql/mysql.h ]]; echo $$((!$$?))" 
                    OUTPUT_VARIABLE DCLIB_MYSQL)
endif()

if (NOT DEFINED DCLIB_TERMINAL)
    set(DCLIB_TERMINAL 0)
endif()

if (NOT DEFINED DCLIB_NETWORK)
    set(DCLIB_NETWORK ${DCLIB_MYSQL})
endif()

if (NOT DEFINED DCLIB_HAVE_USABLE_SIZE)
    set(DCLIB_HAVE_USABLE_SIZE 0)
    execute_process(COMMAND "grep -sqw malloc_usable_size /usr/include/malloc.h; echo $$((!$$?))" 
                    OUTPUT_VARIABLE DCLIB_HAVE_USABLE_SIZE)
endif()


