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

target_link_libraries(${PROJECT_NAME} PUBLIC m)

find_package(Curses REQUIRED)
target_link_libraries(${PROJECT_NAME} PUBLIC ncurses)

## ----------------------
##    PREPARE VARIABLES
## ----------------------

if (EXISTS /usr/include/mysql/mysql.h)
    set(DCLIB_MYSQL 0)
else()
    target_link_libraries(${PROJECT_NAME} PUBLIC mariadb)   
endif()

if (NOT DEFINED DCLIB_THREAD)
    set(DCLIB_THREAD 0)    
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

add_subdirectory(include)
add_subdirectory(src)
