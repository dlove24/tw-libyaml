# Copyright (c) 2011 David Love
#
# Permission is hereby granted, free of charge, to any person obtaining
# a copy of this software and associated documentation files (the
# "Software"), to deal in the Software without restriction, including
# without limitation the rights to use, copy, modify, merge, publish,
# distribute, sublicense, and/or sell copies of the Software, and to
# permit persons to whom the Software is furnished to do so, subject to
# the following conditions:
#
# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
# LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
# OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
# WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

##
## Project Definition
##

# Project Name
project ( libyaml )

# Set-up CMake
cmake_minimum_required ( VERSION 2.6 )

# Include the checks for library functions
include ( CheckLibraryExists )

# Include the checks for header files
include ( CheckIncludeFiles )

##
## Project Configuration
##

# Set the global configure file
CONFIGURE_FILE( ${CMAKE_CURRENT_SOURCE_DIR}/lib/config/config.h.in ${CMAKE_CURRENT_SOURCE_DIR}/lib/config/config.h )

# Add the global configure file to the search path
include_directories( ${CMAKE_CURRENT_SOURCE_DIR}/lib/config )

# Look for the "assert.h" header file
check_include_files ( assert.h HAVE_ASSERT_H )

# Look for the "inttypes.h" header file
check_include_files ( inttypes.h HAVE_INTTYPES_H )

# Look for the "stdint.h" header file
check_include_files ( stdint.h HAVE_STDINT_H )

# Look for the header files defining the constants for type lengths */
check_include_files ( values.h HAVE_VALUES_H )
check_include_files ( float.h HAVE_FLOAT_H )
check_include_files ( limits.h HAVE_LIMITS_H )

# Check whether we are on a 32-bit or a 64-bit platform for non-Apple
# platforms: Apple platforms have to be checked at compile time

if( NOT APPLE )
   # check 64 bit
   if( CMAKE_SIZEOF_VOID_P EQUALS 4 )
     set( HAVE_64_BIT 0 )
   else( CMAKE_SIZEOF_VOID_P EQUALS 4 )
     set( HAVE_64_BIT 1 )
   endif( CMAKE_SIZEOF_VOID_P EQUALS 4 )

   # check endianness
   include( TestBigEndian )
   test_big_endian( HAVE_BIG_ENDIAN )
   if( HAVE_BIG_ENDIAN )
     set( HAVE_BIG_ENDIAN 1 )
   else( HAVE_BIG_ENDIAN )
     set( HAVE_BIG_ENDIAN 0 )
   endif( HAVE_BIG_ENDIAN )
endif( NOT APPLE )

##
## C Library Sources
##

#
# YAML Library: tw_yaml
#
	
# Add the YAML library headers to the search path
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include)

# Create a library for the C Algorithm data structures, used in the C primitives
add_library( tw_yaml SHARED
	src/api.c
	src/dumper.c
	src/emitter.c
	src/loader.c
	src/parser.c
	src/reader.c
	src/scanner.c
	src/writer.c
	)
	
# Disable warnings about unused return values in the YAML library
set_property( TARGET tw_yaml PROPERTY COMPILE_FLAGS -Wno-unused-value )

# Install the YAML library
install ( TARGETS tw_yaml
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  )
