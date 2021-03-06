cmake_minimum_required(VERSION 2.8.3)
project(object_detection)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
set(OpenCV_DIR /usr/share/OpenCV/)
find_package(catkin REQUIRED COMPONENTS  image_transport roscpp std_msgs geometry_msgs dynamic_reconfigure  cv_bridge tf message_generation)
find_package(OpenCV 3 REQUIRED)
set(CMAKE_BUILD_TYPE Debug)

generate_dynamic_reconfigure_options(
	cfg/object_detection.cfg
)

# set ( CMAKE_CXX_FLAGS "-Ofast -march=native -mno-avx -ggdb")

## System dependencies are found with CMake's conventions
# find_package(Boost REQUIRED COMPONENTS system)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

#######################################
## Declare ROS messages and services ##
#######################################

# Generate messages in the 'msg' folder
 add_message_files(
   DIRECTORY msg
   FILES
   detectedobject.msg
   ObjectWithType.msg
   ObjectWithTypeArray.msg
 )

# Generate services in the 'srv' folder
# add_service_files(
 #  FILES
  # Charging.srv
 #)

## Generate added messages and services with any dependencies listed here
generate_messages(
	DEPENDENCIES
	geometry_msgs 
	std_msgs 
	)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
#   INCLUDE_DIRS include
#  LIBRARIES charging
  CATKIN_DEPENDS message_runtime # image_transport roscpp rospy std_msgs
#  DEPENDS system_lib
)


###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include
  ${catkin_INCLUDE_DIRS}
)

## Declare a cpp library
# add_library(charging
#   src/${PROJECT_NAME}/charging.cpp
# )

## Declare a cpp executable
# add_executable(charging_node src/charging_node.cpp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes

## Specify libraries to link a library or executable target against
# target_link_libraries(charging_node
#   ${catkin_LIBRARIES}
# )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )


## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_charging.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)

add_library(mbzirc_sysmat src/sysmat.cpp)
add_library(mbzirc_segmentation src/CSegmentation.cpp)
add_library(mbzirc_imgtransform src/CTransformation.cpp)
add_library(mbzirc_timer src/CTimer.cpp)

add_executable(mbzirc_detector src/detection.cpp)
add_executable(mbzirc_detect_calib src/calibration.cpp)

add_dependencies(mbzirc_detector ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} ${PROJECT_NAME}_gencfg)
add_dependencies(mbzirc_detect_calib ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS} ${PROJECT_NAME}_gencfg)

target_link_libraries(mbzirc_detector mbzirc_timer mbzirc_sysmat mbzirc_imgtransform mbzirc_segmentation  ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})
target_link_libraries(mbzirc_detect_calib  mbzirc_timer ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

install(TARGETS
		mbzirc_sysmat
		mbzirc_imgtransform
		mbzirc_segmentation
		mbzirc_timer
		mbzirc_detector
		mbzirc_detect_calib
		ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
		LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
		RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
		DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
		FILES_MATCHING PATTERN "*.h"
		PATTERN ".svn" EXCLUDE
)

install(DIRECTORY doc
		DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
