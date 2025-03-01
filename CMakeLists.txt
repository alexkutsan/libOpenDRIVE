cmake_minimum_required(VERSION 3.10)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

project(libOpenDrive VERSION 0.3.0 DESCRIPTION ".xodr library")
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set(CMAKE_CXX_FLAGS "-Wall")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

if(MSVC)
SET(CMAKE_CXX_FLAGS "/EHsc")
SET(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS 1)
else()
set(CMAKE_CXX_FLAGS_DEBUG "-g")
endif()

SET (SOURCES 
    src/Geometries/Arc.cpp
    src/Geometries/CubicSpline.cpp
    src/Geometries/Line.cpp
    src/Geometries/ParamPoly3.cpp
    src/Geometries/RoadGeometry.cpp
    src/Geometries/Spiral.cpp
    src/Geometries/Spiral/odrSpiral.cpp
    src/Junction.cpp
    src/Lane.cpp
    src/LaneSection.cpp
    src/Mesh.cpp
    src/OpenDriveMap.cpp
    src/RefLine.cpp
    src/Road.cpp
    src/RoadMark.cpp
    src/RoadNetworkMesh.cpp
    src/RoadObject.cpp
    src/Signal.cpp
    src/RoutingGraph.cpp
    thirdparty/pugixml/pugixml.cpp
)

add_library(OpenDrive SHARED ${SOURCES}))
target_include_directories(OpenDrive
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include> $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/thirdparty>)

add_library(OpenDriveStatic SHARED ${SOURCES}))
target_include_directories(OpenDrive
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include> $<INSTALL_INTERFACE:include>
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/thirdparty>)
set_target_properties(OpenDriveStatic PROPERTIES POSITION_INDEPENDENT_CODE ON)

add_executable(test-xodr test.cpp)
target_link_libraries(test-xodr OpenDrive)

install(
    TARGETS OpenDrive test-xodr
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
install(DIRECTORY ${CMAKE_SOURCE_DIR}/include/ DESTINATION "include")
install(FILES
    ${CMAKE_SOURCE_DIR}/thirdparty/pugixml/pugixml.hpp
    ${CMAKE_SOURCE_DIR}/thirdparty/pugixml/pugiconfig.hpp DESTINATION "include/pugixml/")

install(TARGETS OpenDrive EXPORT OpenDriveConfig)
install(EXPORT OpenDriveConfig NAMESPACE OpenDrive:: DESTINATION cmake)
