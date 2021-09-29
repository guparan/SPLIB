cmake_minimum_required(VERSION 3.5)
project(SPLIB VERSION 3.0)

include(cmake/environment.cmake)

find_package(SofaFramework REQUIRED)
find_package(SofaPython3 REQUIRED)

SP3_add_python_package(
    SOURCE_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/src/python3/splib
    TARGET_DIRECTORY splib
    )

set(HEADER_FILES src/cpp/splib/initPlugin.h)
set(SOURCE_FILES src/cpp/splib/initPlugin.cpp)

file(GLOB_RECURSE RESOURCE_FILES "*.md" "*.psl" "*.py" "*.pyscn" "*.scn" "*.rst" "*.txt")

add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES} ${RESOURCE_FILES})
target_link_libraries(${PROJECT_NAME} SofaCore)

## Install rules for the library and headers; CMake package configurations files
sofa_create_package_with_targets(
    PACKAGE_NAME SPLIB
    PACKAGE_VERSION ${PROJECT_VERSION}
    TARGETS ${PROJECT_NAME} AUTO_SET_TARGET_PROPERTIES
    INCLUDE_INSTALL_DIR SPLIB
    INCLUDE_SOURCE_DIR "src"
    RELOCATABLE "plugins"
    )

install(DIRECTORY examples DESTINATION share/sofa/${PROJECT_NAME})
install(FILES "README.md" "LICENSE" DESTINATION .)

# Tests
# If SOFA_BUILD_TESTS exists and is OFF, then these tests will be auto-disabled
cmake_dependent_option(SPLIB_BUILD_TESTS "Compile the automatic tests" ON "SOFA_BUILD_TESTS OR NOT DEFINED SOFA_BUILD_TESTS" OFF)
if(SPLIB_BUILD_TESTS)
    add_subdirectory(tests)
endif()

include(cmake/packaging.cmake)
