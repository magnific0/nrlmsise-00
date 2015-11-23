#     Copyright (c) 2010-2012 Delft University of Technology.
#
#    This software is protected by national and international copyright.
#    Any unauthorized use, reproduction or modification is unlawful and
#    will be prosecuted. Commercial and non-private application of the
#    software in any form is strictly prohibited unless otherwise granted
#    by the authors.
#
#    The code is provided without any warranty; without even the implied
#    warranty of merchantibility or fitness for a particular purpose.
#
#    Changelog
#      YYMMDD    Author            Comment
#      151111    J. Geul           Copied generic CMakeLists.txt from Tudat
#                                  adapted for NRLMSISE-00
#

# Specify minimum CMake version.
cmake_minimum_required( VERSION 2.6 )

# Specific project name.
project( NRLMSISE00 )

# Detect if NRLMSISE00 is build standalone or from within project
if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
  message(STATUS "Building ${PROJECT_NAME} standalone.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
else()
  message(STATUS "Building ${PROJECT_NAME} from within ${CMAKE_PROJECT_NAME}.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  include("${CMAKE_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  set(PROJECTROOT ${PROJECTROOT} PARENT_SCOPE)
endif()

# Define the source directory and set custom bin and lib directories for building.
set(SRCROOT ${PROJECT_SOURCE_DIR})

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${SRCROOT}/bin)

# Set the build flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -DINLINE")
set(CMAKE_C_FLAGS_DEBUG          "-g")
set(CMAKE_C_FLAGS_RELEASE        "-O2 -DNDEBUG")
set(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")

# Set CMake build-type. If it not supplied by the user (either directly as an
# argument of through the "UserSettings.txt" file, the default built type is
# "Release".
if((NOT CMAKE_BUILD_TYPE) OR (CMAKE_BUILD_TYPE STREQUAL "Release"))
   message(STATUS "WARNING: building release version!")
   set(CMAKE_BUILD_TYPE Release)
elseif(CMAKE_BUILD_TYPE STREQUAL "Debug")
   message(STATUS "WARNING: building debug version!")
endif()

#Set the header and source files.
set(NRLMSISE00_HEADERS "${SRCROOT}/nrlmsise-00.h")
set(NRLMSISE00_SOURCES "${SRCROOT}/nrlmsise-00.c"
                       "${SRCROOT}/nrlmsise-00_data.c")

# Add static libraries.
add_library(nrlmsise00 STATIC ${NRLMSISE00_SOURCES} ${NRLMSISE00_HEADERS})

# Define option and build test executables
OPTION(NRLMSISE00_WITH_TESTS "Compile NRLMSISE-00 test executables" ON)
IF(NRLMSISE00_WITH_TESTS)
        enable_testing()
        add_executable(nrlmsise-test "${SRCROOT}/nrlmsise-00_test.c")
        target_link_libraries(nrlmsise-test nrlmsise00 m)
        add_test(nrlmsise-test "${SRCROOT}/bin/nrlmsise-test")
ENDIF()

# End of file.
