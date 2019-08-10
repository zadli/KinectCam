cmake_minimum_required(VERSION 3.15)
project(KinectCam)

# Point CMake to the custom modules
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/cmake")

find_package(Kinect 1.8 REQUIRED)
find_package(DirectShow REQUIRED)

set(KinectCam_HEADERS
    KinectCam.h
    VCam.h
)

set(KinectCam_SOURCES
    Dll.cpp
    KinectCam.cpp
    VCam.cpp
)

set(KinectCam_EXTRA
    README.md
    LICENSE
)
source_group("Support Files" FILES ${KinectCam_EXTRA})

add_library(KinectCam MODULE ${KinectCam_HEADERS} ${KinectCam_SOURCES} ${KinectCam_EXTRA})
target_include_directories(KinectCam SYSTEM PRIVATE ${KINECT_INCLUDE_DIR} ${DIRECTSHOW_INCLUDE_DIRS})
target_link_libraries(KinectCam ${KINECT_LIBRARIES} ${DIRECTSHOW_STRMIIDS_LIBRARY})
set_target_properties(KinectCam PROPERTIES SUFFIX .ax COMPILE_FLAGS "/Zc:wchar_t-")