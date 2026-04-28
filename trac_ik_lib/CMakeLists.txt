cmake_minimum_required(VERSION 3.5)
project(trac_ik_lib)

if(CMAKE_COMPILER_IS_GNUCXX OR ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang"))
    set(CMAKE_CXX_FLAGS "-std=c++17 ${CMAKE_CXX_FLAGS}")
endif()

# 1. Dependencias de ROS 2
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(kdl_parser REQUIRED)
find_package(urdf REQUIRED)

# 2. PkgConfig para encontrar NLopt
find_package(PkgConfig REQUIRED)
pkg_check_modules(NLOPT REQUIRED nlopt)

# 3. Eigen 
find_package(Eigen3 REQUIRED)

set(DEPS
  rclcpp
  geometry_msgs
  urdf
  kdl_parser
)

# 4. Crear la librería
add_library(trac_ik SHARED 
  src/kdl_tl.cpp 
  src/nlopt_ik.cpp 
  src/trac_ik.cpp
)

# 5. INCLUDES
target_include_directories(trac_ik PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${NLOPT_INCLUDE_DIRS}
  ${Eigen3_INCLUDE_DIRS}
)


target_include_directories(trac_ik SYSTEM PUBLIC ${NLOPT_INCLUDE_DIRS})

# 6. Vincular librerías
target_link_libraries(trac_ik ${NLOPT_LIBRARIES})
ament_target_dependencies(trac_ik ${DEPS})

# --- Instalación y Exportación ---
install(DIRECTORY include/ DESTINATION include)

install(TARGETS trac_ik
  EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

ament_export_libraries(trac_ik)
ament_export_targets(export_${PROJECT_NAME})
ament_export_dependencies(${DEPS})
ament_export_include_directories(include)

ament_package()

