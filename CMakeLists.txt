cmake_minimum_required(VERSION 2.8.3)
project(gstreamer_catkin)

find_package(catkin_simple REQUIRED)
catkin_simple()

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)

ExternalProject_Add(gstreamer_src
  URL https://gstreamer.freedesktop.org/src/gstreamer/gstreamer-1.12.4.tar.xz
  UPDATE_COMMAND ""
  CONFIGURE_COMMAND cd ../gstreamer_src && ./configure --with-pic --prefix=${CATKIN_DEVEL_PREFIX}
  BUILD_COMMAND cd ../gstreamer_src && make -j8
  INSTALL_COMMAND cd ../gstreamer_src && make install -j8
)

cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} gstreamer_src)
target_link_libraries(${PROJECT_NAME}
  ${CATKIN_DEVEL_PREFIX}/lib/libgstbase-1.0${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libgstcheck-1.0${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libgstcontroller-1.0${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libgstnet-1.0${CMAKE_SHARED_LIBRARY_SUFFIX}
  ${CATKIN_DEVEL_PREFIX}/lib/libgstreamer-1.0${CMAKE_SHARED_LIBRARY_SUFFIX}
)

cs_install()

cs_export(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
  CFG_EXTRAS gstreamer-extras.cmake)
