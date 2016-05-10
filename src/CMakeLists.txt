set(APP_NAME drc-apriltags-driver)

#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

add_executable(${APP_NAME} apriltags_driver.cpp)

pods_use_pkg_config_packages(${APP_NAME}
  apriltags
  drc-utils
  opencv
  yaml-cpp
)

pods_install_executables(${APP_NAME})
