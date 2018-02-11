cmake_minimum_required(VERSION 2.6.4)
project(svn-crawler)

find_program(APR_1_CONFIG apr-1-config)
if(APR_1_CONFIG) 
    execute_process(COMMAND apr-1-config --cppflags --includes
        OUTPUT_VARIABLE APR_CFLAGS
        OUTPUT_STRIP_TRAILING_WHITESPACE)

    execute_process(COMMAND apr-1-config --link-ld
        OUTPUT_VARIABLE RAW_APR_LIBS
        OUTPUT_STRIP_TRAILING_WHITESPACE)

    string(STRIP "${RAW_APR_LIBS}" APR_LIBS)
endif(APR_1_CONFIG)
find_path(LIBAPR_LIBRARY_INCLUDE apr.h HINTS ${CMAKE_INSTALL_PREFIX} PATH_SUFFIXES include/apr-1.0)

find_library(LIBSVN_RA_LIBRARY NAMES svn_ra-1 DOC "libsvn_ra")
find_path(LIBSVN_RA_LIBRARY_INCLUDE svn_ra.h HINTS ${CMAKE_INSTALL_PREFIX} PATH_SUFFIXES include/subversion-1)

find_library(LIBSVN_DELTA_LIBRARY NAMES svn_delta-1 DOC "libsvn_delta")

find_library(LIBSVN_SUBR_LIBRARY NAMES svn_subr-1 DOC "libsvn_subr")

find_library(LIBSVN_CLIENT_LIBRARY NAMES svn_client-1 DOC "libsvn_client")
find_path(LIBSVN_CLIENT_LIBRARY_INCLUDE svn_client.h HINTS ${CMAKE_INSTALL_PREFIX} PATH_SUFFIXES include/subversion-1)

add_executable(svn-crawler main.c)
include_directories(${LIBSVN_RA_LIBRARY_INCLUDE} ${LIBSVN_CLIENT_LIBRARY_INCLUDE} ${LIBAPR_LIBRARY_INCLUDE})
set_target_properties(svn-crawler PROPERTIES COMPILE_FLAGS "-g -O0" LINK_FLAGS "-g -O0")
target_link_libraries(svn-crawler ${LIBSVN_RA_LIBRARY} ${LIBSVN_DELTA_LIBRARY} ${LIBSVN_SUBR_LIBRARY} ${LIBSVN_CLIENT_LIBRARY} ${APR_LIBS})
