# Build for markturn

cmake_minimum_required(VERSION 3.18.4)

project(markturn)

find_package(CURL REQUIRED)
find_package(LibXml2 REQUIRED)

option(LINK_STATIC "Build with static library" ON)

# Set path for include files 
# @NOTE find_package() does work for at least yaml, so why not use it?
# @NOTE At least comment on why you are not using it
find_path(YAMLCPP_DIR NAMES "yaml.h" PATHS /usr/include/yaml-cpp/ REQUIRED)
message("-- YAML-CPP DIR: " ${YAMLCPP_DIR})

find_path(JSONCPP_DIR NAMES "json/json.h" PATHS /usr/include/jsoncpp /usr/include/ REQUIRED)
message("-- JSONCPP DIR: " ${JSONCPP_DIR})

set(LIB_INCLUDE_DIRS ${LIBXML2_INCLUDE_DIR} ${YAMLCPP_DIR} ${JSONCPP_DIR})

# Build markturn
set(MAIN_SOURCE "src/markturn.cpp")
add_executable(markturn ${MAIN_SOURCE})
if(LINK_STATIC)
    message("-- Static Library Used")
    target_link_libraries(markturn PUBLIC markturn_static)
else()
    message("-- Shared Library Used")
    target_link_libraries(markturn PUBLIC markturn_shared)
endif()
install(TARGETS markturn RUNTIME)

# Libmarkturn Libraries
file(GLOB LIBRARY_SOURCES "src/*Format.cpp" "src/libmarkturn.cpp")

# Object Library 
add_library(markturnobj OBJECT ${LIBRARY_SOURCES})
target_include_directories(markturnobj PUBLIC ${LIB_INCLUDE_DIRS})

# Static markturn_static library
add_library(markturn_static STATIC $<TARGET_OBJECTS:markturnobj>)
target_link_libraries(markturn_static PUBLIC ${LIBXML2_LIBRARY})
install(TARGETS markturn_static LIBRARY)

# Shared markturn_shared Library
add_library(markturn_shared SHARED $<TARGET_OBJECTS:markturnobj>)
target_link_libraries(markturn_shared PUBLIC ${LIBXML2_LIBRARY} ${YAML_CPP_LIBRARIES} ${JSONCPP_LIBRARY})
install(TARGETS markturn_shared LIBRARY)

# markturn test program
enable_testing()
file(GLOB TEST_SOURCES "test/*.cpp")
foreach(TESTFILE ${TEST_SOURCES})
    message("-- Test for ${TESTFILE} created successfully")
    get_filename_component(TEST_NAME ${TESTFILE} NAME_WLE)

    add_executable(${TEST_NAME} ${TESTFILE})
    target_include_directories(${TEST_NAME} PUBLIC ${LIBXML2_INCLUDE_DIR})
    target_include_directories(${TEST_NAME} PUBLIC src)
    target_link_libraries(${TEST_NAME} PUBLIC ${LIBXML2_LIBRARY})
    add_test(NAME ${TEST_NAME} COMMAND ${TEST_NAME})

    #message("-- Added Test ${TEST_NAME} command ${TEST_NAME} program ${TESTFILE}")
endforeach()
# @NOTE Comment each block of code
add_custom_target(run DEPENDS markturn COMMAND ./markturn -h)

set(CPACK_PACKAGE_NAME "markturn")
set(CPACK_PACKAGE_VERSION_MAJOR "1")
set(CPACK_PACKAGE_VERSION_MINOR "0")
set(CPACK_PACKAGE_VERSION_PATCH "0")
include(CPack)
