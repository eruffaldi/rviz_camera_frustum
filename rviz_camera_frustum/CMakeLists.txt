cmake_minimum_required(VERSION 2.8.3)
project(rviz_camera_frustum)

## Find catkin and any catkin packages on which
## this package depends at build time
find_package(catkin REQUIRED COMPONENTS roscpp rospy roslib std_msgs shape_msgs geometry_msgs rviz pluginlib class_loader cv_bridge)

## This plugin includes Qt widgets, so we must include Qt like so:
find_package(Qt4 COMPONENTS QtCore QtGui REQUIRED)
include(${QT_USE_FILE})

find_package(OpenGL REQUIRED)

## I prefer the Qt signals and slots to avoid defining "emit", "slots",
## etc because they can conflict with boost signals, so define QT_NO_KEYWORDS here.
add_definitions(-DQT_NO_KEYWORDS)

catkin_package(
  LIBRARIES
    vigir_ocs_rviz_plugin_camera_frustum_display_custom
    ${OPENGL_LIBRARIES}
  INCLUDE_DIRS
    src
    ${OPENGL_INCLUDE_DIR}
  CATKIN_DEPENDS
    roscpp 
    rospy
    roslib
    std_msgs
    shape_msgs
    geometry_msgs 
    rviz 
    pluginlib 
    class_loader
    cv_bridge
)

include_directories(src
    ${catkin_INCLUDE_DIRS})

include_directories(SYSTEM
                    ${OPENGL_INCLUDE_DIR}
                    ${QT_INCLUDE_DIR})
                    
link_directories(${catkin_LIBRARY_DIRS})

# Convert the Qt Signals and Slots for QWidget events
qt4_wrap_cpp(MOC_SOURCES src/camera_frustum_display_custom.h)

include_directories(${catkin_INCLUDE_DIRS} ${CMAKE_CURRENT_BINARY_DIR})

set(VIGIR_BOUNDING_OBJECT_LIB_NAME vigir_ocs_rviz_plugin_camera_frustum_display_custom)

add_library(${VIGIR_BOUNDING_OBJECT_LIB_NAME}_core src/camera_frustum_display_custom.cpp  ${MOC_SOURCES})
target_link_libraries(${VIGIR_BOUNDING_OBJECT_LIB_NAME}_core ${catkin_LIBRARIES} ${QT_LIBRARIES})

add_dependencies(${VIGIR_BOUNDING_OBJECT_LIB_NAME}_core ${catkin_EXPORTED_TARGETS})

add_library(${VIGIR_BOUNDING_OBJECT_LIB_NAME} src/plugin_init.cpp)
target_link_libraries(${VIGIR_BOUNDING_OBJECT_LIB_NAME} ${VIGIR_BOUNDING_OBJECT_LIB_NAME}_core ${catkin_LIBRARIES})

add_dependencies(${VIGIR_BOUNDING_OBJECT_LIB_NAME} ${catkin_EXPORTED_TARGETS})

install(TARGETS ${VIGIR_BOUNDING_OBJECT_LIB_NAME} ${VIGIR_BOUNDING_OBJECT_LIB_NAME}_core
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

install(FILES
  camera_frustum_display_custom_plugin_description.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})


set(CPACK_GENERATOR "DEB")
set(CPACK_DEBIAN_PACKAGE_NAME ros-indigo-sssa-${PROJECT_NAME})
set(CPACK_DEBIAN_PACKAGE_VERSION ${PROJECT_SOVERSION})
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Emanuele Ruffaldi <e.ruffaldi@sssup.it>")
set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "Test")
set(CPACK_DEBIAN_PACKAGE_SECTION "main")

include(CPack)
