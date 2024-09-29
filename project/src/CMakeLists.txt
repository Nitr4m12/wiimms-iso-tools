add_subdirectory(libwbfs)
add_subdirectory(lzma)

set(SOURCES
    ${CMAKE_CURRENT_SOURCE_DIR}/crypt.h
    ${CMAKE_CURRENT_SOURCE_DIR}/iso-interface.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-bzip2.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-std.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-file.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-sf.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-lzma.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-wdf.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-wia.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-ciso.c
    ${CMAKE_CURRENT_SOURCE_DIR}/lib-gcz.c
    ${CMAKE_CURRENT_SOURCE_DIR}/match-pattern.c
    ${CMAKE_CURRENT_SOURCE_DIR}/patch.c
    ${CMAKE_CURRENT_SOURCE_DIR}/titles.c
    ${CMAKE_CURRENT_SOURCE_DIR}/wbfs-interface.c
)

target_sources(wit 
PRIVATE 
    ${SOURCES}
    ${CMAKE_CURRENT_SOURCE_DIR}/wit.c
    ${CMAKE_CURRENT_SOURCE_DIR}/wit-mix.c
    # ${CMAKE_CURRENT_SOURCE_DIR}/wwt+wit-cmd.c
)

if(MSVC)
    target_sources(wit PRIVATE winapi.c)
endif(MSVC)

target_sources(wwt 
PRIVATE
    ${SOURCES}
    ${CMAKE_CURRENT_SOURCE_DIR}/wwt.c
    # ${CMAKE_CURRENT_SOURCE_DIR}/wwt+wit-cmd.c
)

target_sources(wdf 
PRIVATE
    ${SOURCES}
    ${CMAKE_CURRENT_SOURCE_DIR}/wdf.c
)

if(HAVE_FUSE EQUAL 1)
    target_sources(wfuse
    PRIVATE
        ${SOURCES}
        ${CMAKE_CURRENT_SOURCE_DIR}/wfuse.c
    )
endif(HAVE_FUSE EQUAL 1)


