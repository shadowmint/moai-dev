# Copyright 2012 Douglas Linder
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required (VERSION 2.8)
project(lua)

# Import macros
file(GLOB PROJECT_MACROS "${PROJECT_SOURCE_DIR}/../../cmake/*.cmake")
foreach(ITEM ${PROJECT_MACROS})
  include(${ITEM})
endforeach()

# Config
set(PROJECT_KEY "lua-5.1.3")

# Source and includes
set(PROJECT_REAL_PATH "${PROJECT_SOURCE_DIR}/../../../../3rdparty/${PROJECT_KEY}")
get_filename_component(PROJECT_REAL_PATH ${PROJECT_REAL_PATH} ABSOLUTE)
file(GLOB PROJECT_SOURCES "${PROJECT_REAL_PATH}/src/*.c")
set(PROJECT_INCLUDE_DIRS "${PROJECT_REAL_PATH}/src")
filter_list("${PROJECT_SOURCES}" "lua.c;luac.c" PROJECT_SOURCES)
if (${DEBUG_MESSAGES})
  message("Importing ${PROJECT_KEY} from ${PROJECT_REAL_PATH}")
endif()

# Win32 config
if(WIN32)
  add_definitions( "-DLUA_API=__declspec(dllexport)" )
endif(WIN32)

# Build library (static)
include_directories(${PROJECT_INCLUDE_DIRS})
add_library(lua STATIC ${PROJECT_SOURCES})
target_link_libraries(lua m)

# Export libraries
set(PROJECT_LIBRARIES lua;m)
set(PROJECT_NAME "LUA")

CONFIGURE_FILE(luaConfig.cmake.in "${PROJECT_BINARY_DIR}/luaConfig.cmake")
