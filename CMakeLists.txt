cmake_minimum_required(VERSION 2.8.12)
project(example)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

find_package(LIBIGL QUIET)

if (NOT LIBIGL_FOUND)
   message(FATAL_ERROR "libigl not found --- You can download it using: \n git clone --recursive https://github.com/libigl/libigl.git ${PROJECT_SOURCE_DIR}/../libigl")
endif()


### libIGL options: choose between header only and compiled static library
# Header-only is preferred for small projects. For larger projects the static build
# considerably reduces the compilation times
option(LIBIGL_USE_STATIC_LIBRARY "Use LibIGL as static library" OFF)

### add a customizable menu bar
option(LIBIGL_WITH_NANOGUI     "Use Nanogui menu"   OFF)

### libIGL options: choose your dependencies (by default everything is OFF except opengl) ###
option(LIBIGL_WITH_VIEWER           "Use OpenGL viewer"  ON)
option(LIBIGL_WITH_NANOGUI          "Use Nanogui menu"   OFF)
option(LIBIGL_WITH_CGAL             "Use CGAL"           OFF)
option(LIBIGL_WITH_BOOLEAN          "Use Cork boolean"   OFF) # This option is not supported yet
option(LIBIGL_WITH_COMISO           "Use CoMiso"         OFF)
option(LIBIGL_WITH_EMBREE           "Use Embree"         OFF)
option(LIBIGL_WITH_LIM              "Use LIM"            OFF)
option(LIBIGL_WITH_MATLAB           "Use Matlab"         OFF) # This option is not supported yet
option(LIBIGL_WITH_MOSEK            "Use MOSEK"          OFF) # This option is not supported yet
option(LIBIGL_WITH_BBW              "Use BBW"            OFF)
option(LIBIGL_WITH_OPENGL_AND_PNG   "Use OpenGL"         OFF)
option(LIBIGL_WITH_TETGEN           "Use Tetgen"         OFF)
option(LIBIGL_WITH_TRIANGLE         "Use Triangle"       OFF)
option(LIBIGL_WITH_XML              "Use XML"            OFF)

if(LIBIGL_WITH_CGAL) # Do not remove or move this block, the cgal build system fails without it
  find_package(CGAL REQUIRED)
  set(CGAL_DONT_OVERRIDE_CMAKE_FLAGS TRUE CACHE BOOL "CGAL's CMAKE Setup is super annoying ")
  include(${CGAL_USE_FILE})
endif()

### Adding libIGL: choose the path to your local copy libIGL ###
### This is going to compile everything you requested ###
add_subdirectory("${LIBIGL_INCLUDE_DIR}/../shared/cmake" "libigl")

### Compilation flags: adapt to your needs ###
if(MSVC)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP /bigobj") ### Enable parallel compilation
  #set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG ${CMAKE_BINARY_DIR} )
  #set(CMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE ${CMAKE_BINARY_DIR} )
else()
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11") #### Libigl requires a modern C++ compiler that supports c++11
  #set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "." )
endif()


### Add your project files
file(GLOB SRCFILES *.cpp)
add_executable(${PROJECT_NAME}_bin ${SRCFILES} ${LIBIGL_EXTRA_SOURCES})
target_include_directories(${PROJECT_NAME}_bin PRIVATE ${LIBIGL_INCLUDE_DIRS})
target_compile_definitions(${PROJECT_NAME}_bin PRIVATE ${LIBIGL_DEFINITIONS})
target_link_libraries(${PROJECT_NAME}_bin ${LIBIGL_LIBRARIES} ${LIBIGL_EXTRA_LIBRARIES})
