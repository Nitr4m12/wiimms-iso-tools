cmake_minimum_required(VERSION 3.15)
project(wiimms-iso-tools VERSION 3.05 LANGUAGES C ASM)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/../bin)

# ==========================
#   GLOBAL COMPILE OPTIONS
# ==========================

add_compile_options(
    -fomit-frame-pointer 
    -fno-strict-aliasing 
    -funroll-loops
    -Wall 
    -Wno-deprecated-declarations 
    -Wno-unused-function 
    -Wno-unused-but-set-variable
    -Wno-format 
    -Wno-parentheses 
    -Wno-dangling-pointer
    -Wno-address-of-packed-member 
    -Wno-return-local-addr 
    -Wno-stringop-overflow
    -Wno-maybe-uninitialized
    -O3
)

if(LINUX OR MSVC)
    add_compile_options(-Wno-format-truncation)
endif(LINUX OR MSVC)

if(NOT APPLE)
    add_compile_options(-static-libgcc)
endif(NOT APPLE)

# ==============================
#   GLOBAL COMPILE DEFINITIONS
# ==============================

add_compile_definitions(AUTHOR="Dirk Clemens")
add_compile_definitions(TOOLSET_SHORT="WIT")
add_compile_definitions(TOOLSET_LONG="Wiimms ISO Tools")

add_compile_definitions(WIT_SHORT="wit")
add_compile_definitions(WIT_LONG="Wiimms ISO Tool")
add_compile_definitions(WWT_SHORT="wwt")
add_compile_definitions(WWT_LONG="Wiimms WBFS Tool")
add_compile_definitions(WDF_SHORT="wdf")
add_compile_definitions(WDF_LONG="Wiimms WDF Tool")
add_compile_definitions(WFUSE_SHORT="wfuse")
add_compile_definitions(WFUSE_LONG="Wiimms FUSE Tool")

add_compile_definitions(VERSION_NUM="3.05a")
execute_process(COMMAND date "+%s %Y-%m-%d %T" OUTPUT_VARIABLE DATE)
add_compile_definitions(DATE="2024-09-26")

add_compile_definitions(URI_HOME="https://wit.wiimm.de/")
add_compile_definitions(URI_DOWNLOAD="https://wit.wiimm.de/download")
add_compile_definitions(URI_FILE="https://wit.wiimm.de/file")

add_compile_definitions(SHARE_PATH="/usr/local/share/wit")

set(BETA_VERSION 0) # 0:off  -1:"beta"  >0:"beta#"
add_compile_definitions(BETA_VERSION="${BETA_VERSION}")
if (BETA_VERSION EQUAL 0)
    add_compile_definitions(URI_REPOS="http://opensvn.wiimm.de/wii/trunk/wiimms-iso-tools/")
    add_compile_definitions(URI_VIEWVC="https://wit.wiimm.de/r/viewvc")
else()
    add_compile_definitions(URI_REPOS="http://opensvn.wiimm.de/wii/branches/public/wiimms-iso-tools/")
    add_compile_definitions(URI_VIEWVC="https://wit.wiimm.de/r/viewvc-beta")
endif()

add_compile_definitions(URI_WDF="https://wit.wiimm.de/r/wdf")
add_compile_definitions(URI_CISO="https://wit.wiimm.de/r/ciso")
add_compile_definitions(URI_QTWITGUI="https://wit.wiimm.de/r/qtwitgui")
add_compile_definitions(URI_WIIBAFU="https://wit.wiimm.de/r/wiibafu")
add_compile_definitions(URI_MACWITGUI="https://wit.wiimm.de/r/mac-witgui")
add_compile_definitions(URI_GBATEMP="http://gbatemp.net/index.php?showtopic=182236")
add_compile_definitions(URI_DOWNLOAD_I386="${URI_DOWNLOAD}/${DISTRIB_I386}")
add_compile_definitions(URI_DOWNLOAD_X86_64="${URI_DOWNLOAD}/${DISTRIB_X86_64}")
add_compile_definitions(URI_DOWNLOAD_MAC="${URI_DOWNLOAD}/${DISTRIB_MAC}")
add_compile_definitions(URI_DOWNLOAD_CYGWIN="${URI_DOWNLOAD}/${DISTRIB_CYGWIN}")
add_compile_definitions(URI_TITLES="http://gametdb.com/titles.txt")

add_compile_definitions(DOWNLOAD_DIR="/n/www/wit.wiimm.de/download")
add_compile_definitions(EXPORT_DIR="/cygdrive/n/wit/wiimms-iso-tools/bin")

#execute_process(COMMAND bash ${CMAKE_CURRENT_SOURCE_DIR}/setup.sh WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

add_compile_definitions(LOG_FORMAT="\\33[36;1m*** %7s %-17s %s\\33[0m\n")
add_compile_definitions(LOGFORMAT_CC="\\33[33;1m*** %7s %-17s %s\\33[0m\n")
add_compile_definitions(LOGFORMAT_TOOL="\\33[35;1m*** %7s %-17s %s\\33[0m\n")
add_compile_definitions(LOGFORMAT_INST="\\33[32;1m*** %7s %-17s %s\\33[0m\n")

if (BETA_VERSION EQUAL 0)
    set(BETA_SUFFIX "")
elseif (BETA_VERSION EQUAL -1)
    set(BETA_SUFFIX .beta)
else()
    set(BETA_SUFFIX .beta${BETA_VERSION})
endif()

add_compile_definitions(VERSION="${VERSION_NUM}${BETA_SUFFIX}")
add_compile_definitions(REVISION="8638")
add_compile_definitions(REVISION_NUM=8638)
add_compile_definitions(REVISION_NEXT=8638)
add_compile_definitions(SYSTEM="x86_64")
add_compile_definitions(SYSTEM2="x86_64")

add_compile_definitions(LARGE_FILES)
add_compile_definitions(_FILE_OFFSET_BITS=64)
add_compile_definitions(WIT)
add_compile_definitions(DEBUG_ASSERT)
add_compile_definitions(EXTENDED_ERRORS=1)
add_compile_definitions(_7ZIP_ST=1)
add_compile_definitions(_LZMA_PROB32=1)

# ==========================
#   GLOBAL LIBRARY LINKAGE  
# ==========================

find_package(Curses REQUIRED)
link_libraries(ncurses)

find_package(BZip2 REQUIRED)
link_libraries(BZip2::BZip2)
add_compile_definitions(HAVE_BZIP)

find_package(OpenSSL REQUIRED)
link_libraries(OpenSSL::SSL)
add_compile_definitions(HAVE_OPENSSL)

# ==========================
#       CREATE TARGETS       
# ==========================

add_executable(wit)
add_executable(wwt)
add_executable(wdf)
add_subdirectory(src)

target_compile_features(wit PUBLIC c_std_99)
target_compile_features(wwt PUBLIC c_std_99)
target_compile_features(wdf PUBLIC c_std_99)

# ==========================
#   LOCAL LIBRARY LINKAGE  
# ==========================

add_subdirectory(dclib)
target_link_libraries(wit PUBLIC dclib)
target_link_libraries(wwt PUBLIC dclib)
target_link_libraries(wdf PUBLIC dclib)

target_link_libraries(wit PUBLIC wbfs)
target_link_libraries(wwt PUBLIC wbfs)
target_link_libraries(wdf PUBLIC wbfs)
