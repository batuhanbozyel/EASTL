#-------------------------------------------------------------------------------------------
# Copyright (C) Electronic Arts Inc.  All rights reserved.
#-------------------------------------------------------------------------------------------
cmake_minimum_required(VERSION 3.13)
project(EASTL CXX)

#-------------------------------------------------------------------------------------------
# Defines 
#-------------------------------------------------------------------------------------------
add_definitions(-D_CHAR16T)

#-------------------------------------------------------------------------------------------
# Header only library 
#-------------------------------------------------------------------------------------------
add_library(EABase INTERFACE) 

#-------------------------------------------------------------------------------------------
# Include dirs
#-------------------------------------------------------------------------------------------
target_include_directories(EABase INTERFACE include)

#-------------------------------------------------------------------------------------------
# Installation
#-------------------------------------------------------------------------------------------
install(DIRECTORY include/EABase DESTINATION include)

#-------------------------------------------------------------------------------------------
# Options
#-------------------------------------------------------------------------------------------
option(EASTL_STD_ITERATOR_CATEGORY_ENABLED "Enable compatibility with std:: iterator categories" OFF)
option(EASTL_DISABLE_APRIL_2024_DEPRECATIONS "Enable use of API marked for removal in April 2024." OFF)
option(EASTL_DISABLE_SEPT_2024_DEPRECATIONS "Enable use of API marked for removal in September 2024." OFF)
option(EASTL_DISABLE_APRIL_2025_DEPRECATIONS "Enable use of API marked for removal in April 2025." OFF)

#-------------------------------------------------------------------------------------------
# Compiler Flags
#-------------------------------------------------------------------------------------------
set (CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_CURRENT_SOURCE_DIR}/scripts/CMake")
include(CommonCppFlags)

#-------------------------------------------------------------------------------------------
# Library definition
#-------------------------------------------------------------------------------------------
file(GLOB EASTL_SOURCES "source/*.cpp")
file(GLOB_RECURSE EASTL_HEADERS "include/EASTL/**.h")
add_library(EASTL ${EASTL_SOURCES} ${EASTL_HEADERS})
target_compile_features(EASTL PUBLIC cxx_std_14)

# include both source and headers in the files tab in Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} PREFIX "Header Files" FILES ${EASTL_HEADERS})

if (MSVC)
    set(EASTL_NATVIS_DIR "doc")
    set(EASTL_NATVIS_FILE "${EASTL_NATVIS_DIR}/EASTL.natvis")
    target_sources(EASTL INTERFACE
        $<INSTALL_INTERFACE:${EASTL_NATVIS_FILE}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/${EASTL_NATVIS_FILE}>
    )
endif()

#-------------------------------------------------------------------------------------------
# Defines
#-------------------------------------------------------------------------------------------
add_definitions(-D_CHAR16T)
add_definitions(-D_CRT_SECURE_NO_WARNINGS)
add_definitions(-D_SCL_SECURE_NO_WARNINGS)
add_definitions(-DEASTL_OPENSOURCE=1)
if (EASTL_STD_ITERATOR_CATEGORY_ENABLED)
  add_definitions(-DEASTL_STD_ITERATOR_CATEGORY_ENABLED=1)
endif()

#-------------------------------------------------------------------------------------------
# Include dirs
#-------------------------------------------------------------------------------------------
target_include_directories(EASTL PUBLIC include)

#-------------------------------------------------------------------------------------------
# Dependencies
#-------------------------------------------------------------------------------------------
target_link_libraries(EASTL EABase)

#-------------------------------------------------------------------------------------------
# Deprecations
#-------------------------------------------------------------------------------------------
if(EASTL_DISABLE_APRIL_2024_DEPRECATIONS)
  target_compile_definitions(EASTL PUBLIC EA_DEPRECATIONS_FOR_2024_APRIL=EA_DISABLED)
endif()
if(EASTL_DISABLE_SEPT_2024_DEPRECATIONS)
  target_compile_definitions(EASTL PUBLIC EA_DEPRECATIONS_FOR_2024_SEPT=EA_DISABLED)
endif()
if(EASTL_DISABLE_APRIL_2025_DEPRECATIONS)
  target_compile_definitions(EASTL PUBLIC EA_DEPRECATIONS_FOR_2025_APRIL=EA_DISABLED)
endif()


#-------------------------------------------------------------------------------------------
# Installation
#-------------------------------------------------------------------------------------------
install(TARGETS EASTL DESTINATION lib)
install(DIRECTORY include/EASTL DESTINATION include)

if (MSVC)
    install(FILES ${EASTL_NATVIS_FILE} DESTINATION ${EASTL_NATVIS_DIR})
endif()
