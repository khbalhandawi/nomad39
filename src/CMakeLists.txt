cmake_minimum_required(VERSION 3.10)

project (nomadlib LANGUAGES CXX VERSION ${NOMAD_VERSION})

# Disable in-source builds to prevent source tree corruption.
if(" ${CMAKE_SOURCE_DIR}" STREQUAL " ${CMAKE_BINARY_DIR}")
    message(FATAL_ERROR "FATAL: In-source builds are not allowed.
    You should create a separate directory for build files.")
endif()

# check compiler version
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    # require at least gcc 4
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 4)
        message(FATAL_ERROR "GCC version < 4 has not been tested for Nomad")
    endif()
elseif (("${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang") 
    OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))
    # require at least clang 5
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 5)
        message(FATAL_ERROR "Clang version has not been tested for Nomad")
    endif()
elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
    # require at least 15.0 (MSVC 2017) for C++14 support
    if (MSVC_TOOLSET_VERSION VERSION_LESS 141)
        message(FATAL_ERROR "MSVC version ${CMAKE_CXX_COMPILER_VERSION} has not been tested for Nomad")
    endif()
else()
    message(WARNING "You are using an unsupported compiler. Compilation has only been tested with Clang, GCC, and MSVC.")
endif()

# specify the C++ standard
set(CMAKE_CXX_STANDARD 14)

# list your source files here
set(NOMADLIB_SOURCES 
    ./Algo_Parameters.cpp
    ./Barrier.cpp
    ./Cache.cpp
    ./Cache_File_Point.cpp
    ./Cache_Point.cpp
    ./Cache_Search.cpp
    ./Clock.cpp
    ./Direction.cpp
    ./Directions.cpp
    ./Display.cpp
    ./Double.cpp
    ./Evaluator.cpp
    ./Evaluator_Control.cpp
    ./Eval_Point.cpp
    ./Extended_Poll.cpp
    ./GMesh.cpp
    ./LH_Search.cpp
    ./L_Curve.cpp
    ./Mads.cpp
    ./Model_Sorted_Point.cpp
    ./Model_Stats.cpp
    ./Multi_Obj_Evaluator.cpp
    ./NelderMead_Search.cpp
    ./NelderMead_Simplex_Eval_Point.cpp
    ./nomad.cpp
    ./OrthogonalMesh.cpp
    ./Parameters.cpp
    ./Parameter_Entries.cpp
    ./Parameter_Entry.cpp
    ./Pareto_Front.cpp
    ./Pareto_Point.cpp
    ./Phase_One_Evaluator.cpp
    ./Phase_One_Search.cpp
    ./Point.cpp
    ./Priority_Eval_Point.cpp
    ./Quad_Model.cpp
    ./Quad_Model_Evaluator.cpp
    ./Quad_Model_Search.cpp
    ./Random_Pickup.cpp
    ./RNG.cpp
    ./Sgtelib_Model_Evaluator.cpp
    ./Sgtelib_Model_Manager.cpp
    ./Sgtelib_Model_Search.cpp
    ./Signature.cpp
    ./Slave.cpp
    ./SMesh.cpp
    ./Speculative_Search.cpp
    ./Stats.cpp
    ./TrendMatrix_Line_Search.cpp
    ./utils.cpp
    ./Variable_Group.cpp
    ./VNS_Search.cpp
    ./XMesh.cpp
)

# list your source files here
set(NOMADLIB_HEADERS
    ./Algo_Parameters.hpp
    ./Barrier.hpp
    ./Cache.hpp
    ./Cache_File_Point.hpp
    ./Cache_Point.hpp
    ./Cache_Search.hpp
    ./Clock.hpp
    ./Direction.hpp
    ./Directions.hpp
    ./Display.hpp
    ./Double.hpp
    ./Evaluator.hpp
    ./Evaluator_Control.hpp
    ./Eval_Point.hpp
    ./Extended_Poll.hpp
    ./GMesh.hpp
    ./LH_Search.hpp
    ./L_Curve.hpp
    ./Mads.hpp
    ./Model_Sorted_Point.hpp
    ./Model_Stats.hpp
    ./Multi_Obj_Evaluator.hpp
    ./NelderMead_Search.hpp
    ./NelderMead_Simplex_Eval_Point.hpp
    ./nomad.hpp
    ./OrthogonalMesh.hpp
    ./Parameters.hpp
    ./Parameter_Entries.hpp
    ./Parameter_Entry.hpp
    ./Pareto_Front.hpp
    ./Pareto_Point.hpp
    ./Phase_One_Evaluator.hpp
    ./Phase_One_Search.hpp
    ./Point.hpp
    ./Priority_Eval_Point.hpp
    ./Quad_Model.hpp
    ./Quad_Model_Evaluator.hpp
    ./Quad_Model_Search.hpp
    ./Random_Pickup.hpp
    ./RNG.hpp
    ./Sgtelib_Model_Evaluator.hpp
    ./Sgtelib_Model_Manager.hpp
    ./Sgtelib_Model_Search.hpp
    ./Signature.hpp
    ./Slave.hpp
    ./SMesh.hpp
    ./Speculative_Search.hpp
    ./Stats.hpp
    ./TrendMatrix_Line_Search.hpp
    ./utils.hpp
    ./Variable_Group.hpp
    ./VNS_Search.hpp
    ./XMesh.hpp
)

# load classic directories for unix systems
include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

# build shared library nomadlib
add_library (nomadlib SHARED ${NOMADLIB_SOURCES})
target_include_directories(nomadlib PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/../ext/sgtelib/include>
    )

# export symbols on Windows
if (WIN32)
    target_compile_definitions(nomadlib PRIVATE -DDLL_EXPORTS)
endif()

set_target_properties(nomadlib  PROPERTIES 
                                VERSION ${NOMAD_VERSION}
                                SOVERSION ${NOMAD_VERSION_MAJOR}.${NOMAD_VERSION_MINOR})


# installing executables and libraries
install(TARGETS nomadlib 
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}  
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/
)

if(MSVC)  
    install(FILES $<TARGET_PDB_FILE:nomadlib> DESTINATION ${CMAKE_INSTALL_LIBDIR} OPTIONAL)  
endif() 

# install header files
install(FILES ${NOMADLIB_HEADERS}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/nomadlib
)

target_link_libraries(nomadlib PRIVATE sgtelib)