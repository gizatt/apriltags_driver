set(APP_NAME drc-apriltags-driver)

#set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

add_executable(${APP_NAME} apriltags_driver.cpp LcmWrapper.cpp)

pods_use_pkg_config_packages(${APP_NAME}
  apriltags
  bot2-core
  bot2-param-client
  lcmtypes_bot2-core
  yaml-cpp
  eigen3
)

find_package(OpenCV REQUIRED)
target_link_libraries(${APP_NAME} ${OpenCV_LIBS})

find_package(Threads REQUIRED)
target_link_libraries(${APP_NAME} ${CMAKE_THREAD_LIBS_INIT})

pods_install_executables(${APP_NAME})
