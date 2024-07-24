cmake_minimum_required(VERSION 3.12)

project(pipes
        VERSION 0.0.1
        DESCRIPTION "A header only C++ library to create pipelines operating on collections"
        HOMEPAGE_URL "https://github.com/joboccara/pipes")

set(BUILD_TESTING OFF)

include(GNUInstallDirs)

add_library(${PROJECT_NAME} INTERFACE)
add_library(joboccara::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_include_directories(${PROJECT_NAME}
                           INTERFACE
                           $<BUILD_INTERFACE:${${PROJECT_NAME}_SOURCE_DIR}/include>
                           $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_14)

# Install configuration
if (NOT DEFINED CMAKE_INSTALL_LIBDIR)
  set(CMAKE_INSTALL_LIBDIR "lib")
endif()
if (NOT DEFINED CMAKE_INSTALL_BINDIR)
  set(CMAKE_INSTALL_BINDIR "bin")
endif()
if (NOT DEFINED CMAKE_INSTALL_INCLUDEDIR)
  set(CMAKE_INSTALL_INCLUDEDIR "include")
endif()

# Include files
install(
  TARGETS     ${PROJECT_NAME}
  EXPORT      ${PROJECT_NAME}_targets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(FILES     ${PROJECT_SOURCE_DIR}/LICENSE                 DESTINATION share/licenses/pipes)

# pkg-config files
configure_file(
  ${PROJECT_SOURCE_DIR}/cmake/${PROJECT_NAME}.pc.in
  ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.pc 
  @ONLY )
install(FILES ${CMAKE_BINARY_DIR}/${PROJECT_NAME}.pc DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)

# CMake config files
set(TARGETS_FILE "${PROJECT_NAME}-targets.cmake")
set(CONFIG_TARGETS_FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake")
set(EXPORT_DESTINATION_DIR share/${PROJECT_NAME})
install(EXPORT ${PROJECT_NAME}_targets
        FILE ${TARGETS_FILE}
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION ${EXPORT_DESTINATION_DIR})
configure_file(
  ${PROJECT_SOURCE_DIR}/cmake/${PROJECT_NAME}_config.cmake.in
  ${CONFIG_TARGETS_FILE}
  @ONLY )
install(FILES ${CONFIG_TARGETS_FILE} DESTINATION ${EXPORT_DESTINATION_DIR})

include(CTest)

if(BUILD_TESTING)
    add_subdirectory(tests)
endif()
