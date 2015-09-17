cmake_minimum_required(VERSION 2.8.7 FATAL_ERROR)
#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME cpm-emokit)
set(CPM_LIB_TARGET_NAME ${CPM_MODULE_NAME})

if ((DEFINED CPM_DIR) AND (DEFINED CPM_UNIQUE_ID) AND (DEFINED CPM_TARGET_NAME))
set(CPM_LIB_TARGET_NAME ${CPM_TARGET_NAME})
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CPM_DIR})
include(CPM)
else()
set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
find_package(Git)
if(NOT GIT_FOUND)
  message(FATAL_ERROR "CPM requires Git.")
endif()
if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
  message(STATUS "Cloning repo (https://github.com/iauns/cpm)")
  execute_process(
    COMMAND "${GIT_EXECUTABLE}" clone https://github.com/iauns/cpm ${CPM_DIR}
    RESULT_VARIABLE error_code
    OUTPUT_QUIET ERROR_QUIET)
  if(error_code)
    message(FATAL_ERROR "CPM failed to get the hash for HEAD")
  endif()
endif()
include(${CPM_DIR}/CPM.cmake)
endif()

# Include CPM modules or externals here (with CPM_AddModule).

CPM_InitModule(${CPM_MODULE_NAME})

set(EMOKIT_DIR ${CMAKE_CURRENT_SOURCE_DIR}/3rdParty/emokit)
CPM_EnsureRepoIsCurrent(
  TARGET_DIR ${EMOKIT_DIR}
  GIT_REPOSITORY "https://github.com/openyou/emokit/"
  USE_CACHING TRUE
  )

# list(APPEND CMAKE_MODULE_PATH "${EMOKIT_DIR}/cmake_modules")
set(CMAKE_SOURCE_DIR "${EMOKIT_DIR}")
add_subdirectory(${EMOKIT_DIR})

CPM_ExportAdditionalIncludeDir("${EMOKIT_DIR}/include")
CPM_ExportAdditionalLibraryTarget("emokit")
