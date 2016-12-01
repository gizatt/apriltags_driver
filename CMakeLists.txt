cmake_minimum_required(VERSION 2.6.0)

foreach(_prefix_path ${CMAKE_PREFIX_PATH})
  set(ENV{PKG_CONFIG_PATH} $ENV{PKG_CONFIG_PATH}:${_prefix_path}/lib/pkgconfig)
endforeach()

set(POD_NAME drc_apriltags_driver)
include(cmake/pods.cmake)

add_subdirectory(src)
