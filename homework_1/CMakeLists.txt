cmake_minimum_required(VERSION 3.16)
project(helloworld_cli LANGUAGES CXX)

if(NOT DEFINED BUILD_NUMBER)
  if(DEFINED ENV{GITHUB_RUN_NUMBER})
    set(BUILD_NUMBER $ENV{GITHUB_RUN_NUMBER})
  else()
    set(BUILD_NUMBER 0)
  endif()
endif()

add_executable(helloworld_cli src/main.cpp)
target_compile_features(helloworld_cli PRIVATE cxx_std_17)
target_compile_definitions(helloworld_cli PRIVATE BUILD_NUMBER=${BUILD_NUMBER})

target_link_options(helloworld_cli PRIVATE -static-libstdc++ -static-libgcc)

include(GNUInstallDirs)
install(TARGETS helloworld_cli RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

include(CTest)          
if(BUILD_TESTING)
  enable_testing()       
  add_test(NAME prints_version COMMAND $<TARGET_FILE:helloworld_cli>)
  set_tests_properties(prints_version PROPERTIES
    PASS_REGULAR_EXPRESSION "Version: ${BUILD_NUMBER}.*Hello, World!")
endif()

set(CPACK_GENERATOR "DEB")
set(CPACK_PACKAGE_NAME "helloworld")
set(CPACK_PACKAGE_VERSION "0.0.${BUILD_NUMBER}")
set(CPACK_PACKAGE_CONTACT "Your Name naumenko33301@gmail.com")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Matthew")
set(CPACK_DEBIAN_PACKAGE_SECTION "utils")
set(CPACK_DEBIAN_PACKAGE_PRIORITY "optional")

set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-Linux")
include(CPack)
