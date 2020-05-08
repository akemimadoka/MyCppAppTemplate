cmake_minimum_required(VERSION 3.15)

project(MyCppAppTemplate CXX)

if(NOT EXISTS "${CMAKE_BINARY_DIR}/conan.cmake")
    message(STATUS "Downloading conan.cmake")
    file(DOWNLOAD "https://raw.githubusercontent.com/akemimadoka/cmake-conan/develop/conan.cmake"
                    "${CMAKE_BINARY_DIR}/conan.cmake" SHOW_PROGRESS
                    STATUS _download_status)
    list(GET _download_status 0 _download_status_code)
    list(GET _download_status 1 _download_status_msg)
    if(NOT _download_status_code EQUAL 0)
        file(REMOVE "${CMAKE_BINARY_DIR}/conan.cmake")
        message(FATAL_ERROR "Failed to download conan.cmake, status code is ${_download_status_code}, msg is ${_download_status_msg}")
    endif()
endif()

include(${CMAKE_BINARY_DIR}/conan.cmake)

conan_cmake_run(CONANFILE conanfile.txt
                BASIC_SETUP CMAKE_TARGETS
                BUILD missing)

file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS
    src/*.cpp
    src/*.h)

add_executable(MyCppAppTemplate ${SOURCE_FILES})

target_compile_features(MyCppAppTemplate PRIVATE cxx_std_20)
target_link_libraries(MyCppAppTemplate PRIVATE ${CONAN_TARGETS})

install(TARGETS MyCppAppTemplate
    RUNTIME DESTINATION bin)
