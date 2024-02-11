cmake_minimum_required(VERSION 3.9)
project(implot CXX)

set(CMAKE_CXX_STANDARD 11)

set(CPM_DOWNLOAD_LOCATION "${CMAKE_BINARY_DIR}/cmake/CPM.cmake")
if(NOT (EXISTS ${CPM_DOWNLOAD_LOCATION}))
    file(DOWNLOAD https://github.com/cpm-cmake/CPM.cmake/releases/download/v0.38.3/CPM.cmake ${CPM_DOWNLOAD_LOCATION}
        EXPECTED_HASH SHA256=cc155ce02e7945e7b8967ddfaff0b050e958a723ef7aad3766d368940cb15494)
endif()
include(${CPM_DOWNLOAD_LOCATION})

CPMAddPackage(
    NAME imgui
    GITHUB_REPOSITORY GameDevDeps/imgui
    GIT_TAG imgui/v1.90.2)
find_package(imgui REQUIRED)

set(IMPLOT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/implot.cpp ${CMAKE_CURRENT_SOURCE_DIR}/implot_items.cpp)
add_library(implot.implot STATIC ${IMPLOT_SOURCES})
target_include_directories(implot.implot PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include/implot>)
target_link_libraries(implot.implot PUBLIC imgui.imgui)
set_target_properties(implot.implot PROPERTIES OUTPUT_NAME implot EXPORT_NAME implot)
add_library(implot::implot ALIAS implot.implot)

install(
    FILES ${PROJECT_SOURCE_DIR}/implot.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/implot)
install(
    TARGETS implot.implot
    EXPORT implot
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    FRAMEWORK DESTINATION Library/Frameworks)
install(EXPORT implot
    NAMESPACE implot::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/implot
    FILE implot.cmake)
