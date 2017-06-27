set(APP_NAME drc-apriltags-driver)

#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

add_executable(${APP_NAME} apriltags_driver.cpp LcmWrapper.cpp)

find_package(libbot QUIET)
if(NOT libbot_FOUND)
  set(libbot_pkg bot2-core bot2-param-client lcmtypes_bot2-core)
else()
  list(APPEND libbot_LIBRARIES libbot::bot2-param-client)
  # Find glib-2.0 and gthread-2.0 which are not automatically found by CMake.
  # gthread-2.0 finds glib-2.0
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(gthread-2.0 gthread-2.0 IMPORTED_TARGET)
  if(gthread-2.0_FOUND)
    list(APPEND libbot_LIBRARIES PkgConfig::gthread-2.0)
  else()
    message(FATAL_ERROR "libbot requires gthread-2.0 and it was not found.")
  endif()
endif()
find_package(yaml-cpp QUIET)
if(NOT yaml-cpp_FOUND)
  set(yaml-cpp_pkg yaml-cpp)
endif()
find_package(Eigen3 QUIET)
if(NOT Eigen3_FOUND)
  set(eigen3_pkg eigen3)
endif()
target_link_libraries(${APP_NAME} ${Eigen3_LIBRARIES} ${yaml-cpp_LIBRARIES} ${libbot_LIBRARIES})

pods_use_pkg_config_packages(${APP_NAME}
  apriltags
  ${libbot_pkg}
  ${yaml-cpp_pkg}
  ${eigen3_pkg}
)

find_package(OpenCV REQUIRED)
target_link_libraries(${APP_NAME} ${OpenCV_LIBS})

find_package(Threads REQUIRED)
target_link_libraries(${APP_NAME} ${CMAKE_THREAD_LIBS_INIT})

pods_install_executables(${APP_NAME})
