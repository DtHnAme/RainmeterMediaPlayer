cmake_minimum_required(VERSION 3.16.0 FATAL_ERROR)

set(CMAKE_SYSTEM_VERSION 10.0 CACHE STRING "" FORCE)

project(MediaPlayer CXX)

################################################################################
# Set target arch type if empty. Visual studio solution generator provides it.
################################################################################
if(NOT CMAKE_VS_PLATFORM_NAME)
    set(CMAKE_VS_PLATFORM_NAME "x64")
endif()
message("${CMAKE_VS_PLATFORM_NAME} architecture in use")

if(NOT ("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64"
     OR "${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86"))
    message(FATAL_ERROR "${CMAKE_VS_PLATFORM_NAME} arch is not supported!")
endif()

################################################################################
# Global configuration types
################################################################################
set(CMAKE_CONFIGURATION_TYPES
    "Debug"
    "Release"
    "Releasestatic"
    CACHE STRING "" FORCE
)

################################################################################
# Global compiler options
################################################################################
if(MSVC)
    # remove default flags provided with CMake for MSVC
    set(CMAKE_CXX_FLAGS "")
    set(CMAKE_CXX_FLAGS_DEBUG "")
    set(CMAKE_CXX_FLAGS_RELEASE "")
    set(CMAKE_CXX_FLAGS_RELEASESTATIC "")
endif()

################################################################################
# Global linker options
################################################################################
if(MSVC)
    # remove default flags provided with CMake for MSVC
    set(CMAKE_EXE_LINKER_FLAGS "")
    set(CMAKE_MODULE_LINKER_FLAGS "")
    set(CMAKE_SHARED_LINKER_FLAGS "")
    set(CMAKE_STATIC_LINKER_FLAGS "")
    set(CMAKE_EXE_LINKER_FLAGS_DEBUG "${CMAKE_EXE_LINKER_FLAGS}")
    set(CMAKE_MODULE_LINKER_FLAGS_DEBUG "${CMAKE_MODULE_LINKER_FLAGS}")
    set(CMAKE_SHARED_LINKER_FLAGS_DEBUG "${CMAKE_SHARED_LINKER_FLAGS}")
    set(CMAKE_STATIC_LINKER_FLAGS_DEBUG "${CMAKE_STATIC_LINKER_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASE "${CMAKE_EXE_LINKER_FLAGS}")
    set(CMAKE_MODULE_LINKER_FLAGS_RELEASE "${CMAKE_MODULE_LINKER_FLAGS}")
    set(CMAKE_SHARED_LINKER_FLAGS_RELEASE "${CMAKE_SHARED_LINKER_FLAGS}")
    set(CMAKE_STATIC_LINKER_FLAGS_RELEASE "${CMAKE_STATIC_LINKER_FLAGS}")
    set(CMAKE_EXE_LINKER_FLAGS_RELEASESTATIC "${CMAKE_EXE_LINKER_FLAGS}")
    set(CMAKE_MODULE_LINKER_FLAGS_RELEASESTATIC "${CMAKE_MODULE_LINKER_FLAGS}")
    set(CMAKE_SHARED_LINKER_FLAGS_RELEASESTATIC "${CMAKE_SHARED_LINKER_FLAGS}")
    set(CMAKE_STATIC_LINKER_FLAGS_RELEASESTATIC "${CMAKE_STATIC_LINKER_FLAGS}")
endif()

################################################################################
# Nuget packages function stub.
################################################################################
function(use_package TARGET PACKAGE VERSION)
    message(WARNING "No implementation of use_package. Create yours. "
                    "Package \"${PACKAGE}\" with version \"${VERSION}\" "
                    "for target \"${TARGET}\" is ignored!")
endfunction()

################################################################################
# Common utils
################################################################################
include(CMake/Utils.cmake)

################################################################################
# Additional Global Settings(add specific info there)
################################################################################
include(CMake/GlobalSettingsInclude.cmake OPTIONAL)

################################################################################
# Use solution folders feature
################################################################################
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

################################################################################
# Sub-projects
################################################################################



























set(PROJECT_NAME MediaPlayer)

################################################################################
# Source groups
################################################################################
set(Header_Files
    "resource.h"
    "src/ImageUtils.h"
    "src/MediaPlayer.h"
    "src/PluginEnums.h"
)
source_group("Header Files" FILES ${Header_Files})

set(Source_Files
    "src/ImageUtils.cpp"
    "src/MediaPlayer.cpp"
    "src/Plugin.cpp"
)
source_group("Source Files" FILES ${Source_Files})

set(ALL_FILES
    ${Header_Files}
    ${Source_Files}
)

################################################################################
# Target
################################################################################
add_library(${PROJECT_NAME} SHARED ${ALL_FILES})

use_props(${PROJECT_NAME} "${CMAKE_CONFIGURATION_TYPES}" "${DEFAULT_CXX_PROPS}")
set(ROOT_NAMESPACE MediaPlayer)

set_target_properties(${PROJECT_NAME} PROPERTIES
    VS_GLOBAL_KEYWORD "Win32Proj"
)
################################################################################
# Target name
################################################################################
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        TARGET_NAME_DEBUG         "${PROJECT_NAME}_debug"
        TARGET_NAME_RELEASE       "${PROJECT_NAME}"
        TARGET_NAME_RELEASESTATIC "${PROJECT_NAME}"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        TARGET_NAME_DEBUG         "${PROJECT_NAME}_debug"
        TARGET_NAME_RELEASE       "${PROJECT_NAME}"
        TARGET_NAME_RELEASESTATIC "${PROJECT_NAME}"
    )
endif()
################################################################################
# Output directory
################################################################################
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        OUTPUT_DIRECTORY_DEBUG         "${CMAKE_SOURCE_DIR}/build/${CMAKE_VS_PLATFORM_NAME}/$<CONFIG>/"
        OUTPUT_DIRECTORY_RELEASE       "${CMAKE_SOURCE_DIR}/build/${CMAKE_VS_PLATFORM_NAME}/$<CONFIG>/"
        OUTPUT_DIRECTORY_RELEASESTATIC "${CMAKE_SOURCE_DIR}/build/${CMAKE_VS_PLATFORM_NAME}/$<CONFIG>/"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        OUTPUT_DIRECTORY_DEBUG         "${CMAKE_SOURCE_DIR}/build/x32/$<CONFIG>/"
        OUTPUT_DIRECTORY_RELEASE       "${CMAKE_SOURCE_DIR}/build/x32/$<CONFIG>/"
        OUTPUT_DIRECTORY_RELEASESTATIC "${CMAKE_SOURCE_DIR}/build/x32/$<CONFIG>/"
    )
endif()
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        INTERPROCEDURAL_OPTIMIZATION_RELEASE       "TRUE"
        INTERPROCEDURAL_OPTIMIZATION_RELEASESTATIC "TRUE"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    set_target_properties(${PROJECT_NAME} PROPERTIES
        INTERPROCEDURAL_OPTIMIZATION_RELEASE       "TRUE"
        INTERPROCEDURAL_OPTIMIZATION_RELEASESTATIC "TRUE"
    )
endif()
################################################################################
# MSVC runtime library
################################################################################
get_property(MSVC_RUNTIME_LIBRARY_DEFAULT TARGET ${PROJECT_NAME} PROPERTY MSVC_RUNTIME_LIBRARY)
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    string(CONCAT "MSVC_RUNTIME_LIBRARY_STR"
        $<$<CONFIG:Releasestatic>:
            MultiThreaded
        >
        $<$<NOT:$<OR:$<CONFIG:Releasestatic>>>:${MSVC_RUNTIME_LIBRARY_DEFAULT}>
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    string(CONCAT "MSVC_RUNTIME_LIBRARY_STR"
        $<$<CONFIG:Releasestatic>:
            MultiThreaded
        >
        $<$<NOT:$<OR:$<CONFIG:Releasestatic>>>:${MSVC_RUNTIME_LIBRARY_DEFAULT}>
    )
endif()
set_target_properties(${PROJECT_NAME} PROPERTIES MSVC_RUNTIME_LIBRARY ${MSVC_RUNTIME_LIBRARY_STR})

################################################################################
# Compile definitions
################################################################################
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        "$<$<CONFIG:Debug>:"
            "_DEBUG"
        ">"
        "$<$<CONFIG:Release>:"
            "NDEBUG"
        ">"
        "$<$<CONFIG:Releasestatic>:"
            "NDEBUG"
        ">"
        "WIN32;"
        "_WINDOWS;"
        "_USRDLL;"
        "MEDIAPLAYER_EXPORTS;"
        "UNICODE;"
        "_UNICODE"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    target_compile_definitions(${PROJECT_NAME} PRIVATE
        "$<$<CONFIG:Debug>:"
            "_DEBUG"
        ">"
        "$<$<CONFIG:Release>:"
            "NDEBUG"
        ">"
        "$<$<CONFIG:Releasestatic>:"
            "NDEBUG"
        ">"
        "WIN32;"
        "_WINDOWS;"
        "_USRDLL;"
        "MEDIAPLAYER_EXPORTS;"
        "UNICODE;"
        "_UNICODE"
    )
endif()

################################################################################
# Compile and link options
################################################################################
if(MSVC)
    if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_compile_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /Oi;
                /sdl;
                /Gy
            >
            $<$<CONFIG:Releasestatic>:
                /Oi;
                /sdl;
                /Gy
            >
            /std:c++17;
            /W3;
            ${DEFAULT_CXX_DEBUG_INFORMATION_FORMAT};
            ${DEFAULT_CXX_EXCEPTION_HANDLING}
        )
    elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
        target_compile_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Release>:
                /Oi;
                /Gy
            >
            $<$<CONFIG:Releasestatic>:
                /Oi;
                /Gy
            >
            /std:c++17;
            /sdl;
            /W3;
            ${DEFAULT_CXX_DEBUG_INFORMATION_FORMAT};
            ${DEFAULT_CXX_EXCEPTION_HANDLING}
        )
    endif()
    if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
        target_link_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Debug>:
                /DEBUG;
                /INCREMENTAL;
                /ASSEMBLYDEBUG
            >
            $<$<CONFIG:Release>:
                /OPT:REF;
                /OPT:ICF;
                /INCREMENTAL:NO
            >
            $<$<CONFIG:Releasestatic>:
                /OPT:REF;
                /OPT:ICF;
                /INCREMENTAL:NO
            >
            /SUBSYSTEM:WINDOWS
        )
    elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
        target_link_options(${PROJECT_NAME} PRIVATE
            $<$<CONFIG:Debug>:
                /DEBUG;
                /INCREMENTAL;
                /ASSEMBLYDEBUG
            >
            $<$<CONFIG:Release>:
                /OPT:REF;
                /OPT:ICF;
                /INCREMENTAL:NO
            >
            $<$<CONFIG:Releasestatic>:
                /OPT:REF;
                /OPT:ICF;
                /INCREMENTAL:NO
            >
            /SUBSYSTEM:WINDOWS
        )
    endif()
endif()

################################################################################
# Post build events
################################################################################
# if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
#    add_custom_command_if(
#        TARGET ${PROJECT_NAME}
#        POST_BUILD
#        COMMANDS
#        COMMAND         $<CONFIG:Debug> copy /Y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>"  "%AppData%\\Rainmeter\\Plugins\\"
#        COMMAND         $<CONFIG:Debug> robocopy "$<SHELL_PATH:${CMAKE_SOURCE_DIR}\\sample_skin" "$ENV{HOMEDRIVE}$ENV{HOMEPATH}\\Documents\\Rainmeter\\Skins\\${PROJECT_NAME}_Debug" /PURGE /E
#        COMMAND         $<CONFIG:Debug> if %errorlevel% leq 1 exit 0 else exit %errorlevel%
#        COMMAND       $<CONFIG:Release> copy /Y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>" "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\x64\\${TARGET_NAME}.dll*"
#        COMMAND       $<CONFIG:Release> robocopy "$<SHELL_PATH:${CMAKE_SOURCE_DIR}\\sample_skin" "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\${PROJECT_NAME}" /PURGE /E
#        COMMAND       $<CONFIG:Release> del "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\${PROJECT_NAME}\\MediaPlayer_Debug.ini"
#        COMMAND       $<CONFIG:Release> if %errorlevel% leq 1 exit 0 else exit %errorlevel%
#        COMMAND $<CONFIG:Releasestatic> xcopy /y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>" "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\static_x64\\${TARGET_NAME}.dll*"
#    )
# elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
#    add_custom_command_if(
#        TARGET ${PROJECT_NAME}
#        POST_BUILD
#        COMMANDS
#        COMMAND         $<CONFIG:Debug> copy /Y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>"  "%AppData%\\Rainmeter\\Plugins\\"
#        COMMAND         $<CONFIG:Debug> robocopy "$<SHELL_PATH:${CMAKE_SOURCE_DIR}\\sample_skin" "$ENV{HOMEDRIVE}$ENV{HOMEPATH}\\Documents\\Rainmeter\\Skins\\${PROJECT_NAME}_Debug" /PURGE /E
#        COMMAND         $<CONFIG:Debug> if %errorlevel% leq 1 exit 0 else exit %errorlevel%
#        COMMAND       $<CONFIG:Release> xcopy /y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>" "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\x32\\${TARGET_NAME}.dll*"
#        COMMAND $<CONFIG:Releasestatic> xcopy /y "$<SHELL_PATH:$<TARGET_FILE:${PROJECT_NAME}>>" "$<SHELL_PATH:${CMAKE_SOURCE_DIR}/>release\\static_x32\\${TARGET_NAME}.dll*"
#    )
# endif()

################################################################################
# Dependencies
################################################################################
if("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x64")
    set(ADDITIONAL_LIBRARY_DEPENDENCIES
        "${CMAKE_SOURCE_DIR}/API/x64/Rainmeter.lib;"
        "Gdiplus;"
        "WindowsApp"
    )
elseif("${CMAKE_VS_PLATFORM_NAME}" STREQUAL "x86")
    set(ADDITIONAL_LIBRARY_DEPENDENCIES
        "${CMAKE_SOURCE_DIR}/API/x32/Rainmeter.lib;"
        "Gdiplus;"
        "WindowsApp"
    )
endif()
target_link_libraries(${PROJECT_NAME} PUBLIC "${ADDITIONAL_LIBRARY_DEPENDENCIES}")

