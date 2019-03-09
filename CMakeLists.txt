# Reference: http://qt-project.org/doc/qt-5.0/qtdoc/cmake-manual.html

cmake_minimum_required(VERSION 3.0)

# Add folder where are supportive functions
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Include Qt basic functions
INCLUDE( External-Qt )

    SET( ENV{CMAKE_PREFIX_PATH} "${PROJECT_BINARY_DIR}/qt/${QT_VERSION}/lib/cmake/Qt5/:$ENV{CMAKE_PREFIX_PATH}" CACHE PATH "" FORCE )
    SET( ENV{CMAKE_PROGRAM_PATH} "${PROJECT_BINARY_DIR}/qt/${QT_VERSION}/bin:$ENV{CMAKE_PROGRAM_PATH}" CACHE PATH "" FORCE )
    SET( ENV{PATH} "${PROJECT_BINARY_DIR}/qt/${QT_VERSION}/bin:$ENV{PATH}" CACHE PATH "" FORCE )
    
    MESSAGE( AUTHOR_WARNING "CMAKE_PREFIX_PATH = $ENV{CMAKE_PREFIX_PATH}" )
    MESSAGE( AUTHOR_WARNING "CMAKE_PROGRAM_PATH = $ENV{CMAKE_PROGRAM_PATH}" )
    MESSAGE( AUTHOR_WARNING "PATH = $ENV{PATH}" )
    
find_package(Qt5 COMPONENTS Widgets REQUIRED)

include(QtCommon)

IF( APPLE )
  set(CMAKE_MACOSX_BUNDLE 1)
  set(CMAKE_INSTALL_RPATH "@executable_path/../Frameworks")
ENDIF()

# Basic information about project

project(example VERSION 1.0)

# Set PROJECT_VERSION_PATCH and PROJECT_VERSION_TWEAK to 0 if not present, needed by add_project_meta
fix_project_version()

# Set additional project information
set(COMPANY "Example")
set(COPYRIGHT "Copyright (c) 2014 Vincent Lee. All rights reserved.")
set(IDENTIFIER "com.example.Example")

set(SOURCE_FILES
    src/main.cc
    src/mainwindow.cc
)

add_project_meta(META_FILES_TO_INCLUDE)

set(RESOURCE_FILES example.qrc)

# source_group("UI Files" FILES ${UI_FILES})

IF (APPLE)
  SET (EXE_NAME "${PROJECT_NAME}_${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}")
ELSE()
  SET( EXE_NAME "${PROJECT_NAME}" )
ENDIF()

add_executable(${EXE_NAME} ${OS_BUNDLE} # Expands to WIN32 or MACOS_BUNDLE depending on OS
    ${SOURCE_FILES} ${META_FILES_TO_INCLUDE} ${RESOURCE_FILES}
)

target_link_libraries(${EXE_NAME} PUBLIC Qt5::Widgets)
install(TARGETS ${EXE_NAME} DESTINATION . COMPONENT Runtime)


qt5_use_modules(${EXE_NAME} Widgets)

# Get reference to deployqt
get_target_property(uic_location Qt5::uic IMPORTED_LOCATION)
get_filename_component( _dir ${uic_location} DIRECTORY)
IF( APPLE )

  set(deployqt "${_dir}/macdeployqt")
  
  if(NOT EXISTS ${deployqt})
    message(FATAL_ERROR "Failed to locate deployqt executable: [${deployqt}]")
  endif()
  
  # Execute deployqt during package creation
  # See https://doc.qt.io/qt-5/osx-deployment.html#macdeploy
  install(CODE "set(deployqt \"${deployqt}\")" COMPONENT Runtime)
  install(CODE [===[
  execute_process(COMMAND "${deployqt}" "${CMAKE_INSTALL_PREFIX}/MyProject.app")
  ]===] COMPONENT Runtime)

  set(CPACK_GENERATOR "DragNDrop")
  include(CPack)

ENDIF()
