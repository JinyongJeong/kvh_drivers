cmake_minimum_required(VERSION 2.8.3)
project(kvh_driver)

find_package(catkin REQUIRED COMPONENTS message_generation roscpp roslint std_msgs)


add_message_files(
  DIRECTORY msg
)

generate_messages(
   DEPENDENCIES
   std_msgs
)

catkin_package(
  CATKIN_DEPENDS
    std_msgs
    message_runtime
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

#set_directory_properties(PROPERTIES INCLUDE_DIRECTORIES "${CMAKE_CURRENT_LIST_DIR}/include;${catkin_INCLUDE_DIRS}")

if (CMAKE_COMPILER_IS_GNUCXX)
  set_directory_properties(PROPERTIES COMPILE_OPTIONS "-std=c++11;-Wall;-Wextra")
endif()

add_library(dsp3000_parser_lib src/dsp3000_parser.cpp)


add_executable(dsp3000 src/dsp3000_node.cpp src/serial_port.cpp include/serial_port.h include/dsp3000_parser.h include/dsp3000_mode.h)


target_link_libraries(dsp3000 ${catkin_LIBRARIES})
target_link_libraries(dsp3000 dsp3000_parser_lib)

add_dependencies(dsp3000 ${catkin_EXPORTED_TARGETS})
add_dependencies(dsp3000_parser_lib ${catkin_EXPORTED_TARGETS})



find_package(Boost REQUIRED COMPONENTS thread system)
target_link_libraries(dsp3000 ${Boost_LIBRARIES})



if(CATKIN_ENABLE_TESTING)
  catkin_add_gtest(dsp3000_parser_utest test/dsp3000_parser_utest.cpp)
  target_link_libraries(dsp3000_parser_utest dsp3000_parser_lib)
endif()

install(TARGETS dsp3000
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
install(TARGETS dsp3000_parser_lib
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

roslint_cpp()
roslint_add_test()
