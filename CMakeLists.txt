cmake_minimum_required(VERSION 3.15)
project(sdk)

include(CheckIncludeFile)

set(CMAKE_CXX_STANDARD 11)
set(SAMP_SDK_ROOT ${CMAKE_CURRENT_SOURCE_DIR})

add_library(sdk SHARED ${SAMP_SDK_ROOT}/amxplugin.cpp ${SAMP_SDK_ROOT}/amxplugin2.cpp ${SAMP_SDK_ROOT}/amx/getch.c)
target_include_directories(sdk PUBLIC ${SAMP_SDK_ROOT} ${SAMP_SDK_ROOT}/amx)

check_include_file(alloca.h HAVE_ALLOCA_H)
if(HAVE_ALLOCA_H)
	target_compile_definitions(sdk PRIVATE -DHAVE_ALLOCA_H)
endif()

check_include_file(inttypes.h HAVE_INTTYPES_H)
if(HAVE_INTTYPES_H)
	target_compile_definitions(sdk PRIVATE -DHAVE_INTTYPES_H)
endif()

check_include_file(malloc.h HAVE_MALLOC_H)
if(HAVE_MALLOC_H)
	target_compile_definitions(sdk PRIVATE -DHAVE_MALLOC_H)
endif()

check_include_file(stdint.h HAVE_STDINT_H)
if(HAVE_STDINT_H)
	target_compile_definitions(sdk PRIVATE -DHAVE_STDINT_H)
endif()

if (WIN32 AND MSVC)
	target_compile_definitions(sdk PRIVATE -D_CRT_SECURE_NO_WARNINGS -DWIN32)
elseif(UNIX)
	target_compile_definitions(sdk PRIVATE -DLINUX)
	target_compile_options(sdk PRIVATE -m32 -std=c++11)
else()
	target_compile_definitions(sdk PRIVATE -DWIN32)
	target_compile_options(sdk PRIVATE -m32 -std=c++11)

	if (CYGWIN)
		# Using cygwin compiler

		target_compile_options(sdk PRIVATE -Wno-ignored-attributes)
	elseif(MINGW)
		# Using mingw
		# Some really stupid but necessary workaround

		target_compile_options(sdk PRIVATE -Wno-attributes -include stddef.h)
		message(STATUS "Using mingw")
	else()
		# Some unsupported platform

		message(FATAL_ERROR "This platform is unsupported")
	endif()
endif()
