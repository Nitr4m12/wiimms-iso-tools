target_sources(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-basics.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-cli.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-color.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-numeric.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-parser.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-file.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-option.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-regex.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-utf8.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-shift-jis.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-vector.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-tables.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-ui.c
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-xdump.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/dclib-debug.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-dol.c 
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-bmg.c 
)

## ==============================================
##    SET UP COMPILE DEFINITIONS AND OPT SOURCES
## ==============================================

if (DCLIB_HAVE_USABLE_SIZE EQUAL 1)
    target_compile_definitions(${PROJECT_NAME} PUBLIC DCLIB_HAVE_USABLE_SIZE=1)
endif()

if (DCLIB_THREAD EQUAL 1)
    target_sources(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_LIST_DIR}/dclib-thread.c)
    target_compile_definitions(${PROJECT_NAME} PUBLIC DCLIB_THREAD=1)
endif()

if (DCLIB_NETWORK EQUAL 1)
    target_sources(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_LIST_DIR}/dclib-network.c ${CMAKE_CURRENT_LIST_DIR}/dclib-punycode.c)
    target_compile_definitions(${PROJECT_NAME} PUBLIC DCLIB_NETWORK)
endif()

if (DCLIB_MYSQL EQUAL 1)
    target_sources(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_LIST_DIR}/dclib-mysql.c)
    target_compile_definitions(${PROJECT_NAME} PUBLIC DCLIB_MYSQL)
endif()

if (DCLIB_TERMINAL EQUAL 1)
    target_sources(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_LIST_DIR}/dclib-terminal.c ${CMAKE_CURRENT_LIST_DIR}/dclib-command.c)
    target_compile_definitions(${PROJECT_NAME} PUBLIC DCLIB_TERMINAL=1)
endif()
