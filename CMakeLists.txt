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

include(ExternalProject)

set(EMOKIT_TARGET_NAME emokit_project)
ExternalProject_Add(${EMOKIT_TARGET_NAME}
  GIT_REPOSITORY "https://github.com/openyou/emokit"
  PATCH_COMMAND ""
  PREFIX "${CMAKE_CURRENT_SOURCE_DIR}"
  INSTALL_DIR ""
  INSTALL_COMMAND ""
  CONFIGURE_COMMAND "cmake" "."
  BUILD_COMMAND "make" "emokit"
  BUILD_IN_SOURCE 1
)
set(EMOKIT_LIBRARY ${CPM_LIB_TARGET_NAME})
add_library(${EMOKIT_LIBRARY} SHARED IMPORTED GLOBAL)
add_dependencies(${EMOKIT_LIBRARY} ${EMOKIT_TARGET_NAME})

ExternalProject_Get_Property(${EMOKIT_TARGET_NAME} SOURCE_DIR)

set(REPO_SOURCE_DIR "${SOURCE_DIR}")
set(REPO_INCLUDE_DIR "${REPO_SOURCE_DIR}/include")

CPM_ExportAdditionalIncludeDir("${REPO_INCLUDE_DIR}")
message(STATUS "including directory: ${REPO_INCLUDE_DIR}")
