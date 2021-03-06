cmake_minimum_required(VERSION 3.5)
project(packml_sm)

set(CMAKE_XX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++")

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic -fPIC -fprofile-arcs -ftest-coverage)
endif()

# prevents weird Qt error
#set(CMAKE_POSITION_INDEPENDENT_CODE ON)


# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)
find_package(rclcpp REQUIRED)
#find_package(rqt_gui REQUIRED)
find_package(rqt_gui_cpp REQUIRED)
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)


#include all directories
include_directories(include 
 ${rclcpp_INCLUDE_DIRS}
 ${rqt_gui_cpp_INCLUDE_DIRS}
 ${Qt5_INCLUDE_DIRS}
 ${CMAKE_CURRENT_BINARY_DIR}
 /usr/include/gtest
 /usr/src/gtest)

link_directories(/usr/lib)

qt5_wrap_cpp(packml_sm_MOCS include/packml_sm/state_machine.h
 include/packml_sm/state.h
 include/packml_sm/transitions.h
 include/packml_sm/events.h 
 include/packml_sm/common.h)

#add libraries
add_library(${PROJECT_NAME} 
 src/state_machine.cpp 
 src/state.cpp 
 src/transitions.cpp 
 ${packml_sm_MOCS})
target_link_libraries(${PROJECT_NAME} ${rqt_gui_cpp_LIBRARIES} Qt5::Widgets Qt5::Core gcov)

#add executables
add_executable(${PROJECT_NAME}_utest test/utest.cpp)
target_link_libraries(${PROJECT_NAME}_utest ${PROJECT_NAME} ${rqt_gui_cpp_LIBRARIES} ${rclcpp_LIBRARIES} Qt5::Widgets Qt5::Core pthread gtest gcov)

#install
install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION lib)

install(TARGETS ${PROJECT_NAME}_utest
  ARCHIVE DESTINATION lib/${PROJECT_NAME}
  LIBRARY DESTINATION lib/${PROJECT_NAME}
  RUNTIME DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY include/${PROJECT_NAME} DESTINATION include)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()

  #ament_add_gtest(${PROJECT_NAME}_utest ${UTEST_SRC_FILES})
  #target_link_libraries(${PROJECT_NAME}_utest ${PROJECT_NAME} ${rclcpp_LIBRARIES} Qt5::Core pthread gtest)

endif()


#Substituting the catkin_package () components:
#INCLUDE_DIRS
ament_export_include_directories(include ${CMAKE_CURRENT_BINARY_DIR})
#LIBRARIES
ament_export_libraries(${PROJECT_NAME})
#CATKIN_DEPENDS
ament_export_dependencies(rqt_gui_cpp)

ament_package()
