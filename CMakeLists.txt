# Copyright (C) 2007-2009 LuaDist.
# Created by Peter Kapec
# Redistribution and use of this file is allowed according to the terms of the MIT license.
# For details see the COPYRIGHT file distributed with LuaDist.
# Please note that the package source code is licensed under its own license.

PROJECT(fcgi)
CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
INCLUDE(dist.cmake)

###########################################################################################

# generating config.h file:
MESSAGE("Configure: FCGI - generating fcgi_config.h:")
	INCLUDE (CheckIncludeFiles)
	INCLUDE (CheckFunctionExists)
	INCLUDE (CheckSymbolExists)
	INCLUDE (CheckLibraryExists)

	CHECK_INCLUDE_FILES ("arpa/inet.h" HAVE_ARPA_INET_H)
	CHECK_INCLUDE_FILES (dlfcn.h HAVE_DLFCN_H)
	CHECK_SYMBOL_EXISTS (fileno "stdio.h" HAVE_FILENO_PROTO)
	CHECK_SYMBOL_EXISTS (fpos_t "stdio.h" HAVE_FILENO_PROTO)
	CHECK_INCLUDE_FILES (inttypes.h HAVE_INTTYPES_H)
	#2DO: HAVE_IOSTREAM_WITHASSIGN_STREAMBUF
	CHECK_LIBRARY_EXISTS(nsl "" "" HAVE_LIBNSL)
	CHECK_LIBRARY_EXISTS(socket "" "" HAVE_LIBSOCKET)
	CHECK_INCLUDE_FILES (limits.h HAVE_LIMITS_H)
	CHECK_INCLUDE_FILES (memory.h HAVE_MEMORY_H)
	CHECK_INCLUDE_FILES (netdb.h HAVE_NETDB_H)
	CHECK_INCLUDE_FILES ("netinet/in.h" HAVE_NETINET_IN_H)
	#2DO: HAVE_PTHREAD
	CHECK_SYMBOL_EXISTS (sun_len "sys/un.h" HAVE_SOCKADDR_UN_SUN_LEN)
	CHECK_SYMBOL_EXISTS (socklen_t "sys/socket.h" HAVE_SOCKLEN)
	CHECK_INCLUDE_FILES (stdint.h HAVE_STDINT_H)
	CHECK_INCLUDE_FILES (stdlib.h HAVE_STDLIB_H)
	#2DO: HAVE_STREAMBUF_CHAR_TYPE
	CHECK_FUNCTION_EXISTS(strerror HAVE_STRERROR)
	CHECK_INCLUDE_FILES (strings.h HAVE_STRINGS_H)
	CHECK_INCLUDE_FILES (string.h HAVE_STRING_H)
	CHECK_INCLUDE_FILES ("sys/param.h" HAVE_SYS_PARAM_H)
	CHECK_INCLUDE_FILES ("sys/socket.h" HAVE_SYS_STAT_H)
	CHECK_INCLUDE_FILES ("sys/stat.h" HAVE_SYS_STAT_H)
	CHECK_INCLUDE_FILES ("sys/time.h" HAVE_SYS_TIME_H)
	CHECK_INCLUDE_FILES ("sys/types.h" HAVE_SYS_TYPES_H)
	CHECK_INCLUDE_FILES (unistd.h HAVE_UNISTD_H)
	#2DO: HAVE_VA_ARG_LONG_DOUBLE_BUG
	SET(PACKAGE "fcgi")
	#2DO: PTHREAD_CREATE_JOINABLE
	CHECK_INCLUDE_FILES("stdlib.h;stdarg.h;string.h;float.h" STDC_HEADERS)
	#USE_LOCKING	
	SET(VERSION "2.4.0")	
	#2DO: /* Define to empty if `const' does not conform to ANSI C. */
	#cmakedefine const
	
	#2DO: /* Define as `__inline' if that's what the C compiler calls it, or to nothing if it is not supported. */
	#cmakedefine inline

	#/* Define to `int' if <sys/types.h> does not define. */
	#undef off_t
	CHECK_SYMBOL_EXISTS(ssize_t "sys/types.h" SSIZE_T)
	IF( NOT SSIZE_T )
		SET(SSIZE_T "int")
	ENDIF()

# all is defined, so generate config.h
CONFIGURE_FILE(${CMAKE_CURRENT_SOURCE_DIR}/fcgi_config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/fcgi_config.h)

MESSAGE("Configure: FCGI - done.")
# end of generating fcgi_config.h file:

ADD_DEFINITIONS(-DHAVE_CONFIG_H -DEOF=4)
INCLUDE_DIRECTORIES (${CMAKE_CURRENT_BINARY_DIR})

###########################################################################################

IF (WIN32 AND NOT CYGWIN)
	SET(SRC libfcgi/os_win32.c)
ELSE ()
	SET(SRC libfcgi/os_unix.c)
ENDIF ()

# Quick environ fix without need to patch
IF (APPLE)
	ADD_DEFINITIONS(-Denviron=\(*_NSGetEnviron\(\)\))
ENDIF ()

SET(SRC ${SRC} 
	libfcgi/fcgi_stdio.c
	libfcgi/fcgiapp.c
	libfcgi/fcgio.cpp
)

INCLUDE_DIRECTORIES(include)

# fcgi lib
ADD_LIBRARY(fcgi SHARED ${SRC})

INSTALL(TARGETS fcgi DESTINATION ${INSTALL_LIB})
INSTALL (FILES LICENSE.TERMS DESTINATION ${INSTALL_DATA})
INSTALL (FILES include/fastcgi.h
	include/fcgi_stdio.h
	include/fcgiapp.h
	include/fcgimisc.h
	include/fcgio.h
	include/fcgios.h DESTINATION ${INSTALL_INC})


INSTALL (DIRECTORY doc/ DESTINATION ${INSTALL_DOC})
INSTALL (DIRECTORY examples/ DESTINATION ${INSTALL_EXAMPLE})
