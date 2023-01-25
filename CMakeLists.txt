cmake_minimum_required(VERSION 3.5)

project(arc_raster_rescue LANGUAGES CXX)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/")
include(GetGitRevisionDescription)
get_git_head_revision(GIT_REFSPEC GIT_HASH)

string(TIMESTAMP MY_TIMESTAMP "%Y-%m-%d %H:%M:%S")

find_package(GDAL REQUIRED)
find_package(ZLIB REQUIRED)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(EXPLORE "Whether or not to print exploratory debugging info useful for building new features.")

add_library(arc_raster_rescue_lib
  src/arr.cpp
)
target_include_directories(arc_raster_rescue_lib PUBLIC ${GDAL_INCLUDE_DIR} include)
target_link_libraries(arc_raster_rescue_lib PRIVATE ZLIB::ZLIB ${GDAL_LIBRARY})
target_compile_options(arc_raster_rescue_lib PRIVATE
    -Wall -pedantic -Wno-unused-variable -DDEBUG -DEXPLORE
)
target_compile_features(arc_raster_rescue_lib PRIVATE cxx_constexpr)
target_compile_definitions(arc_raster_rescue_lib PUBLIC
  GIT_HASH=\"${GIT_HASH}\"
  COMPILE_TIME=\"${MY_TIMESTAMP}\"
)
if(EXPLORE)
  target_compile_definitions(arc_raster_rescue_lib PRIVATE EXPLORE)
endif()



add_executable(arc_raster_rescue
  evaluation/main.cpp
)
target_link_libraries(arc_raster_rescue PRIVATE arc_raster_rescue_lib)
target_compile_options(arc_raster_rescue PRIVATE
  -Wall -pedantic -Wno-unused-variable
)
target_compile_features(arc_raster_rescue PRIVATE cxx_constexpr)

include(GNUInstallDirs)
install(
    TARGETS
        arc_raster_rescue
    DESTINATION
        "${CMAKE_INSTALL_BINDIR}")
