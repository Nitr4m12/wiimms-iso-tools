add_executable(wbfs_test ${CMAKE_CURRENT_LIST_DIR}/src/main.c)

target_link_libraries(wbfs_test PUBLIC wbfs)

add_test(
    NAME default
    COMMAND wbfs_test
)
set_tests_properties(default PROPERTIES WILL_FAIL true)
