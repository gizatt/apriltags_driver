set(APP_NAME drc-apriltags-driver)


# Copied and modified from Modules/FindPkgConfig.cmake in CMake 3.6.0 to
# support older versions of CMake.
function(create_imp_target _prefix)
  unset(_libs)
  unset(_find_opts)

  unset(_search_paths)
  foreach (flag IN LISTS ${_prefix}_LDFLAGS)
    if (flag MATCHES "^-L(.*)")
      # only look into the given paths from now on
      list(APPEND _search_paths ${CMAKE_MATCH_1})
      set(_find_opts HINTS ${_search_paths} NO_DEFAULT_PATH)
      continue()
    endif()
    if (flag MATCHES "^-l(.*)")
      set(_pkg_search "${CMAKE_MATCH_1}")
    else()
      continue()
    endif()

    find_library(pkgcfg_lib_${_prefix}_${_pkg_search}
                 NAMES ${_pkg_search}
                 ${_find_opts})
    list(APPEND _libs "${pkgcfg_lib_${_prefix}_${_pkg_search}}")
  endforeach()

  # only create the target if it is linkable, i.e. no executables
  if (NOT TARGET ${_prefix}
      AND ( ${_prefix}_INCLUDE_DIRS OR _libs OR ${_prefix}_CFLAGS_OTHER ))

    add_library(${_prefix} INTERFACE IMPORTED)

    unset(_props)
    if(${_prefix}_INCLUDE_DIRS)
      set_property(TARGET ${_prefix} PROPERTY
                   INTERFACE_INCLUDE_DIRECTORIES "${${_prefix}_INCLUDE_DIRS}")
    endif()
    if(_libs)
      set_property(TARGET ${_prefix} PROPERTY
                   INTERFACE_LINK_LIBRARIES "${_libs}")
    endif()
    if(${_prefix}_CFLAGS_OTHER)
      set_property(TARGET ${_prefix} PROPERTY
                   INTERFACE_COMPILE_OPTIONS "${${_prefix}_CFLAGS_OTHER}")
    endif()
  endif()
endfunction()


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
  pkg_check_modules(gthread-2.0 gthread-2.0)
  if(gthread-2.0_FOUND)
    create_imp_target(gthread-2.0)
    list(APPEND libbot_LIBRARIES gthread-2.0)
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
