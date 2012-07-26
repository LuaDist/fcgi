# Copyright (C) 2007-2012 LuaDist.
# Created by Peter Kapec, Peter Drahoš
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

project ( fcgi C )
cmake_minimum_required ( VERSION 2.8 )
include ( cmake/dist.cmake )

###########################################################################################

# generating config.h file:
message ( "Configure: FCGI - generating fcgi_config.h:" )
include ( CheckIncludeFiles )
include ( CheckFunctionExists )
include ( CheckSymbolExists )
include ( CheckLibraryExists )

check_include_files ( "arpa/inet.h" HAVE_ARPA_INET_H )
check_include_files ( dlfcn.h HAVE_DLFCN_H )
check_symbol_exists ( fileno "stdio.h" HAVE_FILENO_PROTO )
check_symbol_exists ( fpos_t "stdio.h" HAVE_FILENO_PROTO )
check_include_files ( inttypes.h HAVE_INTTYPES_H )
#2DO: HAVE_IOSTREAM_WITHASSIGN_STREAMBUF
check_library_exists ( nsl "" "" HAVE_LIBNSL )
check_library_exists ( socket "" "" HAVE_LIBSOCKET )
check_include_files ( limits.h HAVE_LIMITS_H )
check_include_files ( memory.h HAVE_MEMORY_H )
check_include_files ( netdb.h HAVE_NETDB_H )
check_include_files ( "netinet/in.h" HAVE_NETINET_IN_H )
#2DO: HAVE_PTHREAD
check_symbol_exists ( sun_len "sys/un.h" HAVE_SOCKADDR_UN_SUN_LEN )
check_symbol_exists ( socklen_t "sys/socket.h" HAVE_SOCKLEN )
check_include_files ( stdint.h HAVE_STDINT_H )
check_include_files ( stdlib.h HAVE_STDLIB_H )
#2DO: HAVE_STREAMBUF_CHAR_TYPE
check_function_exists ( strerror HAVE_STRERROR )
check_include_files ( strings.h HAVE_STRINGS_H )
check_include_files ( string.h HAVE_STRING_H )
check_include_files ( "sys/param.h" HAVE_SYS_PARAM_H )
check_include_files ( "sys/socket.h" HAVE_SYS_STAT_H )
check_include_files ( "sys/stat.h" HAVE_SYS_STAT_H )
check_include_files ( "sys/time.h" HAVE_SYS_TIME_H )
check_include_files ( "sys/types.h" HAVE_SYS_TYPES_H )
check_include_files ( unistd.h HAVE_UNISTD_H )
#2DO: HAVE_VA_ARG_LONG_DOUBLE_BUG
set ( PACKAGE "${DIST_NAME}" )
#2DO: PTHREAD_CREATE_JOINABLE
check_include_files ( "stdlib.h;stdarg.h;string.h;float.h" STDC_HEADERS )
#USE_LOCKING	
set ( VERSION ${DIST_VERSION} )
#2DO: /* Define to empty if `const' does not conform to ANSI C. */
#cmakedefine const

#2DO: /* Define as `__inline' if that's what the C compiler calls it, or to nothing if it is not supported. */
#cmakedefine inline

#/* Define to `int' if <sys/types.h> does not define. */
#undef off_t
check_symbol_exists ( ssize_t "sys/types.h" SSIZE_T )
if ( NOT SSIZE_T )
  set ( SSIZE_T "int" )
endif ( )

# all is defined, so generate config.h
configure_file ( ${CMAKE_CURRENT_SOURCE_DIR}/fcgi_config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/fcgi_config.h )

message ( "Configure: FCGI - done." )
# end of generating fcgi_config.h file:

add_definitions ( -DHAVE_CONFIG_H -DEOF=4 )
include_directories ( ${CMAKE_CURRENT_BINARY_DIR} )

###########################################################################################

if ( WIN32 AND NOT CYGWIN )
  set ( SRC libfcgi/os_win32.c )
else ( )
  set ( SRC libfcgi/os_unix.c )
endif ( )

# Quick environ fix without need to patch
if ( APPLE )
  add_definitions ( -Denviron=\(*_NSGetEnviron\(\)\) )
endif ( )

set ( SRC ${SRC} libfcgi/fcgi_stdio.c libfcgi/fcgiapp.c libfcgi/fcgio.cpp )

include_directories ( include )
add_library ( fcgi ${SRC} )

install_library ( fcgi )
install_data ( LICENSE.TERMS )
install_header ( include/fastcgi.h include/fcgi_stdio.h include/fcgiapp.h include/fcgimisc.h 
  include/fcgio.h include/fcgios.h )
install_doc ( doc/ )
install_example ( examples/ )
