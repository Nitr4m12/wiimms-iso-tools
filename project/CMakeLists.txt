cmake_minimum_required(VERSION 3.15)
project(wiimms-iso-tools VERSION 3.05 LANGUAGES C ASM)

# Set binary output
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/../bin)

# Set shell
set(SHELL bash)

include_directories(${CMAKE_CURRENT_BINARY_DIR}/generated)
include_directories(src)
include_directories(src/ui)

set_property(DIRECTORY APPEND PROPERTY ADDITIONAL_CLEAN_FILES generated)

# =================
#     VARIABLES    
# =================

set(AUTHOR "Dirk Clemens")
set(TOOLSET_SHORT "WIT")
set(TOOLSET_LONG "Wiimms ISO Tools")

set(WIT_SHORT "wit")
set(WIT_LONG "Wiimms ISO Tool")
set(WWT_SHORT "wwt")
set(WWT_LONG "Wiimms WBFS Tool")
set(WDF_SHORT "wdf")
set(WDF_LONG "Wiimms WDF Tool")
set(WFUSE_SHORT "wfuse")
set(WFUSE_LONG "Wiimms FUSE Tool")

set(VERSION_NUM 3.05a)

set(URI_HOME "https://wit.wiimm.de/")
set(URI_DOWNLOAD "https://wit.wiimm.de/download")
set(URI_FILE "https://wit.wiimm.de/file")

set(SHARE_PATH "/usr/local/share/wit")

set(BETA_VERSION 0) # 0:off  -1:"beta"  >0:"beta#"

if (BETA_VERSION EQUAL 0)
    set(BETA_SUFFIX "")
    set(URI_REPOS "http://opensvn.wiimm.de/wii/trunk/wiimms-iso-tools/")
    set(URI_VIEWVC "https://wit.wiimm.de/r/viewvc")
elseif (BETA_VERSION EQUAL -1)
    set(BETA_SUFFIX .beta)
    set(URI_REPOS "http://opensvn.wiimm.de/wii/branches/public/wiimms-iso-tools/")
    set(URI_VIEWVC "https://wit.wiimm.de/r/viewvc-beta")
else()
    set(BETA_SUFFIX .beta${BETA_VERSION})
    set(URI_REPOS "http://opensvn.wiimm.de/wii/branches/public/wiimms-iso-tools/")
    set(URI_VIEWVC "https://wit.wiimm.de/r/viewvc-beta")
endif()

set(VERSION "${VERSION_NUM}${BETA_SUFFIX}")

set(URI_WDF "https://wit.wiimm.de/r/wdf")
set(URI_CISO "https://wit.wiimm.de/r/ciso")
set(URI_QTWITGUI "https://wit.wiimm.de/r/qtwitgui")
set(URI_WIIBAFU "https://wit.wiimm.de/r/wiibafu")
#set(URI_WCDWM https://wit.wiimm.de/r/wcdwm)
#set(URI_WIIJMANAGER https://wit.wiimm.de/r/wiijman)
set(URI_MACWITGUI "https://wit.wiimm.de/r/mac-witgui")
set(URI_GBATEMP "http://gbatemp.net/index.php?showtopic=182236")
set(URI_DOWNLOAD_I386 "${URI_DOWNLOAD}/${DISTRIB_I386}")
set(URI_DOWNLOAD_X86_64 "${URI_DOWNLOAD}/${DISTRIB_X86_64}")
set(URI_DOWNLOAD_MAC "${URI_DOWNLOAD}/${DISTRIB_MAC}")
set(URI_DOWNLOAD_CYGWIN "${URI_DOWNLOAD}/${DISTRIB_CYGWIN}")
set(URI_TITLES "http://gametdb.com/titles.txt")

set(DOWNLOAD_DIR "/n/www/wit.wiimm.de/download")
set(EXPORT_DIR "/cygdrive/n/wit/wiimms-iso-tools/bin")

set(OPT_STATIC "")

# Create our define variables based on cmake variables
# This is done to replicate how the original Makefile works
file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/generated)
execute_process(
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    COMMAND ${SHELL} "setup_cmake.sh" 
    OUTPUT_FILE ${CMAKE_CURRENT_BINARY_DIR}/generated/setup.inc
    RESULT_VARIABLE result
)
if(result EQUAL 0)
    include(${CMAKE_CURRENT_BINARY_DIR}/generated/setup.inc)
    configure_file(setup/version.h.in ${CMAKE_CURRENT_BINARY_DIR}/generated/version.h)
else()
    message(FATAL_ERROR "Something went wrong in the setup process!")
endif()

execute_process(
    WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/generated
    COMMAND ${SHELL} ${CMAKE_CURRENT_SOURCE_DIR}/gen-text-file.sh "${CMAKE_CURRENT_SOURCE_DIR}/text-files/" "logo.inc" "config-paths.inc" "ui-head.inc"
)

# ==============================
#   GLOBAL COMPILE DEFINITIONS
# ==============================

add_compile_definitions(${DEFINES1})
add_compile_definitions(LARGE_FILES)
add_compile_definitions(_FILE_OFFSET_BITS=64)
add_compile_definitions(WIT)
add_compile_definitions(DEBUG_ASSERT)
add_compile_definitions(EXTENDED_ERRORS=1)
add_compile_definitions(_7ZIP_ST=1)
add_compile_definitions(_LZMA_PROB32=1)

# ==========================
#   GLOBAL COMPILE OPTIONS
# ==========================

add_compile_options(
    -fomit-frame-pointer 
    -fno-strict-aliasing 
    -funroll-loops
    
    # Warnings
    -Wall 
    -Wno-deprecated-declarations 
    -Wno-unused-function
    -Wno-format 
    -Wno-parentheses 
    -Wno-dangling-pointer
    -Wno-address-of-packed-member 
    -Wno-return-local-addr 
    -Wno-stringop-overflow
    -Wno-address

    # Optimization Level
    -O3
)

if(LINUX OR MSVC)
    add_compile_options(-Wno-format-truncation -Wno-unused-but-set-variable)
endif(LINUX OR MSVC)

if(NOT APPLE)
    add_compile_options(-Wno-maybe-uninitialized)
endif(NOT APPLE)

if(GCC_VERSION GREATER_EQUAL 7.0)
    add_compile_options(-Wno-format-truncation)
endif(GCC_VERSION GREATER_EQUAL 7.0)


# ==========================
#   GLOBAL LIBRARY LINKAGE  
# ==========================

find_package(BZip2 REQUIRED)
link_libraries(BZip2::BZip2)

find_package(OpenSSL REQUIRED)
link_libraries(OpenSSL::SSL)
link_libraries(crypto)
add_compile_definitions(USE_SHA_LIB)

find_package(ZLIB REQUIRED)
link_libraries(ZLIB::ZLIB)


# ==========================
#       CREATE TARGETS       
# ==========================

add_executable(wit)
add_executable(wwt)
add_executable(wdf)
if(HAVE_FUSE)
    add_executable(wfuse)
endif(HAVE_FUSE)

add_subdirectory(src)

# ===============================
#     TARGET COMPILE FEATURES  
# ===============================

target_compile_features(wit PUBLIC c_std_99)
target_compile_features(wwt PUBLIC c_std_99)
target_compile_features(wdf PUBLIC c_std_99)
if(HAVE_FUSE)
    target_compile_features(wfuse PUBLIC c_std_99)
endif(HAVE_FUSE)

# ===========================
#   TARGET SPECIFIC LINKAGE  
# ===========================

add_subdirectory(dclib)
target_link_libraries(wit PUBLIC dclib)
target_link_libraries(wwt PUBLIC dclib)
target_link_libraries(wdf PUBLIC dclib)

target_link_libraries(wit PUBLIC wbfs)
target_link_libraries(wwt PUBLIC wbfs)
target_link_libraries(wdf PUBLIC wbfs)

if(HAVE_FUSE)
    target_link_libraries(wfuse 
    PUBLIC 
        dclib wbfs fuse
        pthread ldl)
endif(HAVE_FUSE)
