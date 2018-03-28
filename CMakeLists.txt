cmake_minimum_required(VERSION 3.2)

project(BemEmulator_prj VERSION LANGUAGES C CXX)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${PROJECT_SOURCE_DIR}/cmake")

if (WIN32)
  set(OBT_WINDOWS TRUE)
endif()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
  set(OBT_64 ON)
else()
  set(OBT_32 ON)
endif()

if (OBT_64)
  set(BIT_DIR "64")
else()
  set(BIT_DIR "32")
endif()

if (MSVC)
  if (MSVC_VERSION EQUAL 1800)
    set(OBT_VS_DIR "vs2013")
  elseif(MSVC_VERSION EQUAL 1900)
    set(OBT_VS_DIR "vs2015")
  elseif(MSVC_VERSION GREATER 1918)
    set(OBT_VS_DIR "vs2017")
  else()
    error("MSVC targets older than VS2013 (VC12) are not supported")
  endif()
endif()


MESSAGE( STATUS "Looking for libs ... " )
	find_package(OpenAL REQUIRED)
	find_package(Allegro REQUIRED)
MESSAGE( STATUS "All libs found. " )

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)



set(CMAKE_POSITION_INDEPENDENT_CODE ON)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

set(PROJECT_INSTALL_DIR "bin")

add_subdirectory(src)
add_definitions(-BEM -DBEM)

#Copy dll and lib to bin folder

if(MSVC)
    add_custom_target(copy-libs ALL)
    set(BINARY_COPY_DESTINATION "${CMAKE_BINARY_DIR}/bin/$<CONFIG>/")
    set(VISUAL_STUDIO_DEBUGGER_DESTINATION "${CMAKE_BINARY_DIR}/bin/Debug/")
    add_custom_command(TARGET copy-libs POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_directory ${OpenAL_REDIST} ${BINARY_COPY_DESTINATION})
    add_custom_command(TARGET copy-libs POST_BUILD COMMAND ${CMAKE_COMMAND} -E copy_directory ${Allegro_REDIST} ${BINARY_COPY_DESTINATION})
endif()
