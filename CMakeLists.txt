# Copyright (C) 2016 Toyota Motor Corporation
# vim: fileencoding=utf-8 :
cmake_minimum_required(VERSION 2.8.3)
project(hsr_description)

find_package(catkin REQUIRED COMPONENTS rosbash xacro)


file(GLOB _robot_xacros "${PROJECT_SOURCE_DIR}/robots/*.urdf.xacro")
file(GLOB_RECURSE _component_xacros "${PROJECT_SOURCE_DIR}/urdf/*.xacro")

# Generate robot URDFs from each *.urdf.xacro files.
foreach(_xacro ${_robot_xacros})
    get_filename_component(_dir ${_xacro} PATH)
    get_filename_component(_name ${_xacro} NAME_WE)
    set(_urdf ${_dir}/${_name}.urdf)
    add_custom_command(
        OUTPUT ${_urdf}
        DEPENDS ${_xacro} ${_component_xacros}
        COMMAND ${CATKIN_DEVEL_PREFIX}/env.sh rosrun xacro xacro.py ${_xacro} > ${_urdf})
    add_custom_target(${PROJECT_NAME}_${_name}_gen_urdf ALL DEPENDS ${_urdf})
endforeach()

catkin_package(CATKIN_DEPENDS xacro)

install(DIRECTORY launch robots urdf
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/)
