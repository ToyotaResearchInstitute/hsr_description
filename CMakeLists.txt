# Copyright (C) 2016 Toyota Motor Corporation
# vim: fileencoding=utf-8 :
cmake_minimum_required(VERSION 2.8.3)
project(hsr_description)

find_package(catkin REQUIRED COMPONENTS rosbash xacro)


file(GLOB_RECURSE _component_xacros "${PROJECT_SOURCE_DIR}/urdf/*.xacro")

# Macro to generate a URDF from a xacro source
function(generate_urdf)
    cmake_parse_arguments(ARG "" "XACRO;SUFFIX" "DEPENDS;XACRO_ARGS" ${ARGN})
    get_filename_component(_dir ${ARG_XACRO} PATH)
    get_filename_component(_name ${ARG_XACRO} NAME_WE)
    set(_xacro_src ${ARG_XACRO})
    set(_depends "${_xacro_src};${ARG_DEPENDS}")
    set(_urdf "${_dir}/${_name}${ARG_SUFFIX}.urdf")
    add_custom_command(
        OUTPUT ${_urdf}
        DEPENDS ${_depends}
	COMMAND ${CATKIN_DEVEL_PREFIX}/env.sh xacro ${_xacro_src} ${ARG_XACRO_ARGS} --inorder > ${_urdf})
    add_custom_target(${PROJECT_NAME}_${_name}${ARG_SUFFIX}_generate_urdf ALL DEPENDS ${_urdf})
endfunction()

generate_urdf(XACRO "${PROJECT_SOURCE_DIR}/robots/hsrb4s.urdf.xacro"
              DEPENDS ${_component_xacros})
generate_urdf(XACRO "${PROJECT_SOURCE_DIR}/robots/hsrb4s.urdf.xacro"
              SUFFIX ".obj"
              DEPENDS ${_component_xacros}
              XACRO_ARGS use_obj_for_visual:=true use_obj_for_collision:=true)

catkin_package(CATKIN_DEPENDS xacro)

install(DIRECTORY launch robots urdf
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/)
