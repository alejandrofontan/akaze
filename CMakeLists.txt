cmake_minimum_required(VERSION 3.16)
project(akaze)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -O3 -march=native")
set (CMAKE_CXX_STANDARD 17)

add_compile_options(-Wextra -Wpedantic)

set(SRCS_AKAZE
  src/AKAZE.cpp
  src/fed.cpp
  src/nldiffusion_functions.cpp
  src/utils.cpp
)
  
find_package(OpenCV REQUIRED)
find_package(Doxygen)
if (NOT APPLE)
  find_package(OpenMP)
  if(OPENMP_FOUND)
     MESSAGE("OpenMP found")
     SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
     SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  endif(OPENMP_FOUND)
endif ()


add_library(${PROJECT_NAME} SHARED ${SRCS_AKAZE})
target_include_directories(${PROJECT_NAME} PUBLIC 
	"${PROJECT_SOURCE_DIR}"
)

target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES})

configure_file(src/akaze.cmake.in
  "${PROJECT_BINARY_DIR}/akazeConfig.cmake" @ONLY)

install(TARGETS ${PROJECT_NAME}
        RUNTIME DESTINATION  ${CMAKE_INSTALL_PREFIX}/bin
        LIBRARY DESTINATION  ${CMAKE_INSTALL_PREFIX}/lib
        ARCHIVE DESTINATION  ${CMAKE_INSTALL_PREFIX}/lib)
             
install(DIRECTORY
	"${PROJECT_SOURCE_DIR}/akaze"
        DESTINATION  ${CMAKE_INSTALL_PREFIX}/include
        FILES_MATCHING PATTERN "*.h")
                                                  
install(FILES "${PROJECT_BINARY_DIR}/akazeConfig.cmake" 
  DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/akaze/)  
