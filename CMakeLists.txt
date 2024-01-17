cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

set(NOMAD_VERSION_MAJOR 3)
set(NOMAD_VERSION_MINOR 9)
set(NOMAD_VERSION_PATCH 1)  
set(NOMAD_VERSION ${NOMAD_VERSION_MAJOR}.${NOMAD_VERSION_MINOR}.${NOMAD_VERSION_PATCH})

# name of the project
# Need to update when version changes
project (Nomad VERSION ${NOMAD_VERSION_MAJOR}.${NOMAD_VERSION_MINOR})

# specify the C++ standard
set(CMAKE_CXX_STANDARD 14)

#
# Modify the install prefix if not specified on the command
#
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX ${PROJECT_BINARY_DIR} CACHE PATH "..." FORCE) 
endif()
message(STATUS "  Installation prefix set to ${CMAKE_INSTALL_PREFIX}")

set(CMAKE_INSTALL_BINDIR bin)
set(CMAKE_INSTALL_LIBDIR bin)

#-------------------------------------------------------------------------------
#	ADD SUBPROJECTS
#-------------------------------------------------------------------------------

# add subdirectories for each project
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/ext)
#
# Add nomad app directory for building 
#
add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/src)

set(NOMAD_INCLUDE_DIRS src)
set(SGTELIB_INCLUDE_DIRS ext/sgtelib/include)

#-------------------------------------------------------------------------------
#	PROJECT SETTINGS
#-------------------------------------------------------------------------------

set(PROJECT_NAME "nomad") 
cmake_policy(SET CMP0048 NEW)
project(${PROJECT_NAME} VERSION 0.1.0)

set(TARGET_NAME "nomad")

#-------------------------------------------------------------------------------
#	COMPILE FLAGS
#-------------------------------------------------------------------------------

if(MSVC)
    # Use MSVC-specific options
    set(GUI_TYPE WIN32)
    add_compile_options(/W3)
    add_compile_options(/MP)
    add_compile_options("$<$<CONFIG:RELEASE>:/Ox>")
    add_compile_options("$<$<CONFIG:DEBUG>:/Od>")
else()
    # Use GCC/Clang-specific options
    add_compile_options(-std=c++14)
    add_compile_options(-Wall -fPIC)
    add_compile_options("$<$<CONFIG:RELEASE>:-O3>")
    add_compile_options("$<$<CONFIG:DEBUG>:-O0>")
endif()


# # Common compiler flags for all build types
# set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -w -fPIC")

# # Debug build flags
# set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g -O0")
# # Release build flags
# set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O3")

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#-------------------------------------------------------------------------------
#	GENERATE EXECUTABLE
#-------------------------------------------------------------------------------

add_executable(${TARGET_NAME} src/nomad.cpp)
target_include_directories(${TARGET_NAME} PRIVATE
    ${NOMAD_INCLUDE_DIRS}
    ${SGTELIB_INCLUDE_DIRS}
)

install(TARGETS ${TARGET_NAME} 
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}  
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/
)

target_compile_definitions(${TARGET_NAME} PRIVATE VERSION_INFO=${EXAMPLE_VERSION_INFO})
target_link_libraries(${TARGET_NAME} PRIVATE sgtelib nomadlib ${LIBRARIES})

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)