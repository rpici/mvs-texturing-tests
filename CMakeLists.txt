# http://derekmolloy.ie/hello-world-introductions-to-cmake/
# https://crascit.com/2016/01/31/enhanced-source-file-handling-with-target_sources/

# Effective CMake, slide 27 - Daniel Pfeifer:
# https://github.com/boostcon/cppnow_presentations_2017/raw/master/05-19-2017_friday/effective_cmake__daniel_pfeifer__cppnow_05-19-2017.pdf
# "Forget those commands:
#   add_compile_options()
#   include_directories()
#   link_directories()
#   link_libraries()"

cmake_minimum_required( VERSION 3.1 )
project( MVStests )

#===========================================================
# Based on:
#   Building GoogleTest and GoogleMock directly in a CMake project
#   July 25, 2015 by Craig Scott
#   https://crascit.com/2015/07/25/cmake-gtest/

message( "calling configure_file()..." )
message( "CMAKE_BINARY_DIR = ${CMAKE_BINARY_DIR}" )
# Download and unpack catch2 at configure time
configure_file(
    CMakeLists.txt.catch2.in
    ${CMAKE_BINARY_DIR}/catch2-download/CMakeLists.txt
)

message( "execute_process(): ${CMAKE_COMMAND} -G ${CMAKE_GENERATOR} in directory ${CMAKE_BINARY_DIR}/catch2-download..." )

execute_process(
    COMMAND "${CMAKE_COMMAND}" -G "${CMAKE_GENERATOR}" .
    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/catch2-download"
)

message( "execute_process(): ${CMAKE_COMMAND} --build . in directory ${CMAKE_BINARY_DIR}/catch2-download..." )

execute_process(
    COMMAND "${CMAKE_COMMAND}" --build .
    WORKING_DIRECTORY "${CMAKE_BINARY_DIR}/catch2-download"
)

# Add catch2 directly to our build. What targets does this add?
add_subdirectory( "${CMAKE_BINARY_DIR}/catch2-src" "${CMAKE_BINARY_DIR}/catch2-build" )

# Do the catch2 targets carry header search path
# dependencies automatically?
if(CMAKE_VERSION VERSION_LESS 2.8.11)
    include_directories("${catch2_SOURCE_DIR}/include")
endif()

# Now simply link your own targets against gtest, gmock,
# etc. as appropriate
#===========================================================

message( "C++ compiler is ${CMAKE_CXX_COMPILER_ID} version ${CMAKE_CXX_COMPILER_VERSION}" )

set( CMAKE_CXX_STANDARD 11 )

message( "\nCMake version is ${CMAKE_VERSION}.\n" )

add_executable( tests
    CATCH_CONFIG_MAIN.cpp
    local_seam_leveling_tests.cpp
)

target_include_directories( tests PRIVATE "${CMAKE_BINARY_DIR}/catch2-src/single_include" )

