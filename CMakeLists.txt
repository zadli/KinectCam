cmake_minimum_required(VERSION 3.15)
project(KinectCam VERSION 1.0.0)

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

	KinectCam.def # Module definition for exports
)

set(KinectCam_EXTRA
    README.md
    LICENSE
)
source_group("Support Files" FILES ${KinectCam_EXTRA})

add_library(KinectCam MODULE ${KinectCam_HEADERS} ${KinectCam_SOURCES} ${KinectCam_EXTRA})
target_include_directories(KinectCam SYSTEM PRIVATE ${KINECT_INCLUDE_DIR} ${DIRECTSHOW_INCLUDE_DIRS})
target_link_libraries(KinectCam ${KINECT_LIBRARIES} ${DIRECTSHOW_STRMIIDS_LIBRARY} legacy_stdio_definitions.lib)
target_link_libraries(KinectCam winmm debug strmbasd optimized strmbase)
set_target_properties(KinectCam PROPERTIES SUFFIX .ax COMPILE_FLAGS "/Zc:wchar_t-")

# Simple scripts to enable/disable filter registration
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/Reg.cmd" "%WINDIR%\\System32\\regsvr32.exe %~dp0\\KinectCam.ax")
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/UnReg.cmd" "%WINDIR%\\System32\\regsvr32.exe /u %~dp0\\KinectCam.ax")


# Set up package information
set(CPACK_PACKAGE_NAME "KinectCam")
set(CPACK_PACKAGE_VENDOR "Ordered Chaos")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "KinectCam - A DirectShow Filter for the Kinect v1")
SET(CPACK_PACKAGE_DESCRIPTION_FILE "${PROJECT_SOURCE_DIR}/README.md")
set(CPACK_PACKAGE_HOMEPAGE_URL "https://github.com/Deledrius/KinectCam")
set(CPACK_PACKAGE_INSTALL_DIRECTORY "KinectCam")
set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE")

# NSIS-specific configuration
set(CPACK_NSIS_ENABLE_UNINSTALL_BEFORE_INSTALL ON)
set(CPACK_NSIS_EXTRA_INSTALL_COMMANDS "ExecWait '\\\"$SYSDIR\\\\regsvr32.exe\\\" /s \\\"$INSTDIR\\\\KinectCam.ax\\\"'")
set(CPACK_NSIS_EXTRA_UNINSTALL_COMMANDS "ExecWait '\\\"$SYSDIR\\\\regsvr32.exe\\\" /s /u \\\"$INSTDIR\\\\KinectCam.ax\\\"'")
set(CPACK_NSIS_CREATE_ICONS_EXTRA
    "CreateShortCut '$SMPROGRAMS\\\\$STARTMENU_FOLDER\\\\KinectCam Config.lnk' '%LOCALAPPDATA%\\\\KinectCam\\\\config.ini'"
)
set(CPACK_NSIS_DELETE_ICONS_EXTRA
    "Delete '$SMPROGRAMS\\\\$START_MENU\\\\KinectCam Config.lnk'"
)

# Specify files to be installed
install(TARGETS KinectCam LIBRARY DESTINATION .)
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/Reg.cmd"
    "${CMAKE_CURRENT_BINARY_DIR}/UnReg.cmd"
    "README.md"
    "LICENSE"
    DESTINATION .)

include(CPack)