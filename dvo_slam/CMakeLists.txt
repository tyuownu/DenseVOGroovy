cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

rosbuild_init()

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

# dynamic reconfigure
rosbuild_find_ros_package(dynamic_reconfigure)
include(${dynamic_reconfigure_PACKAGE_PATH}/cmake/cfgbuild.cmake)
gencfg()

# messages
rosbuild_genmsg()
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -msse3 -msse2 -std=c++0x")

# VTK library
find_package(VTK REQUIRED)
message("VTK dir is" ${VTK_INCLUDE_DIRS})
include_directories(${VTK_INCLUDE_DIRS})

rosbuild_add_library(${PROJECT_NAME} 
  src/keyframe_tracker.cpp
  src/keyframe_graph.cpp
  src/keyframe_constraint_search.cpp
  src/camera_keyframe_tracking.cpp
  
  src/config.cpp
  
  src/tracking_result_evaluation.cpp
  src/local_map.cpp
  src/local_tracker.cpp
  
  src/constraints/constraint_proposal.cpp
  src/constraints/constraint_proposal_voter.cpp
  src/constraints/constraint_proposal_validator.cpp
  
  src/serialization/map_serializer_interface.cpp
  src/serialization/map_serializer.cpp
  
  src/visualization/graph_visualizer.cpp
  
  src/types.cpp
)

#target_link_libraries(${PROJECT_NAME}
#  g2o_core
#  g2o_solver_dense
#  g2o_solver_csparse
#  g2o_csparse_extension
#  csparse
#  g2o_types_slam3d
#)
target_link_libraries(${PROJECT_NAME}
		g2o_cli
		g2o_core
		g2o_csparse_extension
		g2o_ext_freeglut_minimal
		g2o_incremental
		g2o_interactive
		g2o_interface
		g2o_opengl_helper
		g2o_parser
		g2o_simulator
		g2o_solver_cholmod
		g2o_solver_csparse
		g2o_solver_dense
		g2o_solver_pcg
		g2o_solver_slam2d_linear
		g2o_solver_structure_only
		g2o_stuff
		g2o_types_data
		g2o_types_icp
		g2o_types_sba
		g2o_types_sclam2d
		g2o_types_sim3
		g2o_types_slam2d
		g2o_types_slam2d_addons
		g2o_types_slam3d
		g2o_types_slam3d_addons
		g2o_viewer
		csparse
		tbb
		${VTK_LIBS}
		cxsparse
		vtkRendering
)
rosbuild_add_executable(camera_keyframe_tracker
  src/camera_keyframe_tracker_node.cpp
)

target_link_libraries(camera_keyframe_tracker
  ${PROJECT_NAME}
)
