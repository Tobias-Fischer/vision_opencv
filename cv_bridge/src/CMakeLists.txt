# add library
add_library(${PROJECT_NAME} cv_bridge.cpp rgb_colors.cpp)
add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES} ${Boost_LIBRARIES} ${catkin_LIBRARIES})

install(TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

if(NOT ANDROID)
# add a Boost Python library
find_package(Python COMPONENTS Interpreter Development NumPy)
add_definitions(-DPYTHON3)

add_library(${PROJECT_NAME}_boost module.cpp module_opencv4.cpp)
target_include_directories(${PROJECT_NAME}_boost PUBLIC ${Python_INCLUDE_DIRS} ${Boost_INCLUDE_DIRS} ${Python_NumPy_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME}_boost ${Boost_LIBRARIES}
                                            ${catkin_LIBRARIES}
                                            ${PROJECT_NAME}
)

if(NOT APPLE)
  target_link_libraries(${PROJECT_NAME}_boost ${Python_LIBRARIES})
endif()

set_target_properties(${PROJECT_NAME}_boost PROPERTIES
                      LIBRARY_OUTPUT_DIRECTORY ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_PYTHON_DESTINATION}/${PROJECT_NAME}/boost/
                      RUNTIME_OUTPUT_DIRECTORY ${CATKIN_DEVEL_PREFIX}/${CATKIN_GLOBAL_PYTHON_DESTINATION}/${PROJECT_NAME}/boost/
                      PREFIX ""
)
if(APPLE)
  set_target_properties(${PROJECT_NAME}_boost PROPERTIES
                        SUFFIX ".so")
  set_target_properties(${PROJECT_NAME}_boost PROPERTIES
                        LINK_FLAGS "-undefined dynamic_lookup")
endif()
if(MSVC)
  set_target_properties(${PROJECT_NAME}_boost PROPERTIES
                        SUFFIX ".pyd")
endif()

install(TARGETS ${PROJECT_NAME}_boost DESTINATION ${CATKIN_PACKAGE_PYTHON_DESTINATION}/boost/)
endif()
