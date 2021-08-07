cmake_minimum_required(VERSION 3.7...3.20)

if (${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif ()

if (CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
    message(FATAL_ERROR "Do not build in-source. Please remove CMakeCache.txt and the CMakeFiles/ directory. Then build out-of-source.")
endif ()

option(LUA_BUILD_BINARY "Build lua binary" OFF)
option(LUA_BUILD_AS_CXX "Build lua as C++" ON)
enable_language(CXX)


project(lua
        VERSION 5.4.4
        DESCRIPTION "Lua embeddable scripting language"
        LANGUAGES C
        )

set(LUA_SOURCES_FOLDER "${CMAKE_CURRENT_SOURCE_DIR}/lua")
set(LUA_SOURCES_FILES
        "${LUA_SOURCES_FOLDER}/lapi.c"
        "${LUA_SOURCES_FOLDER}/lcode.c"
        "${LUA_SOURCES_FOLDER}/lctype.c"
        "${LUA_SOURCES_FOLDER}/ldebug.c"
        "${LUA_SOURCES_FOLDER}/ldo.c"
        "${LUA_SOURCES_FOLDER}/ldump.c"
        "${LUA_SOURCES_FOLDER}/lfunc.c"
        "${LUA_SOURCES_FOLDER}/lgc.c"
        "${LUA_SOURCES_FOLDER}/llex.c"
        "${LUA_SOURCES_FOLDER}/lmem.c"
        "${LUA_SOURCES_FOLDER}/lobject.c"
        "${LUA_SOURCES_FOLDER}/lopcodes.c"
        "${LUA_SOURCES_FOLDER}/lparser.c"
        "${LUA_SOURCES_FOLDER}/lstate.c"
        "${LUA_SOURCES_FOLDER}/lstring.c"
        "${LUA_SOURCES_FOLDER}/ltable.c"
        "${LUA_SOURCES_FOLDER}/ltm.c"
        "${LUA_SOURCES_FOLDER}/lundump.c"
        "${LUA_SOURCES_FOLDER}/lvm.c"
        "${LUA_SOURCES_FOLDER}/lzio.c"
        "${LUA_SOURCES_FOLDER}/lauxlib.c"
        "${LUA_SOURCES_FOLDER}/lbaselib.c"
        "${LUA_SOURCES_FOLDER}/lcorolib.c"
        "${LUA_SOURCES_FOLDER}/ldblib.c"
        "${LUA_SOURCES_FOLDER}/liolib.c"
        "${LUA_SOURCES_FOLDER}/lmathlib.c"
        "${LUA_SOURCES_FOLDER}/loadlib.c"
        "${LUA_SOURCES_FOLDER}/loslib.c"
        "${LUA_SOURCES_FOLDER}/lstrlib.c"
        "${LUA_SOURCES_FOLDER}/ltablib.c"
        "${LUA_SOURCES_FOLDER}/lutf8lib.c"
        "${LUA_SOURCES_FOLDER}/linit.c"
        )

if(LUA_BUILD_AS_CXX)
    set_source_files_properties(${LUA_SOURCES_FOLDER} "lua.c" "luac.c" PROPERTIES LANGUAGE CXX )
endif()


message(STATUS "Building Lua version ${PROJECT_VERSION}")

add_library(lua_static STATIC ${LUA_SOURCES_FILES})
add_library(lua_shared SHARED ${LUA_SOURCES_FILES})

target_include_directories(lua_static
        PUBLIC
        ${LUA_SOURCES_FOLDER}
        )
target_include_directories(lua_shared
        PUBLIC
        ${LUA_SOURCES_FOLDER}
        )

add_library(lua::static ALIAS lua_static)
add_library(lua::shared ALIAS lua_shared)

if(UNIX)
    set(LUA_DEFINITIONS)

    if(NOT EMSCRIPTEN)
        find_library(LIBM m)
        if(NOT LIBM)
            message(FATAL_ERROR "libm not found and is required by lua")
        endif()
        target_link_libraries(lua_static INTERFACE ${LIBM})

        list(APPEND LUA_DEFINITIONS LUA_USE_POSIX)
        if(LUA_SUPPORT_DL)
            target_compile_definitions(lua_static PRIVATE "LUA_USE_DLOPEN")
            target_link_libraries(lua_static INTERFACE dl)
        endif()
    endif()

    target_compile_definitions(lua_static
            PUBLIC ${LUA_DEFINITIONS}
            )
    target_compile_options(lua_static
            PRIVATE "-Wall" "-Wextra"
            )
endif()

if(LUA_BUILD_BINARY)
    include(CheckIncludeFile)
    check_include_file("readline/readline.h" HAVE_READLINE_READLINE_H)

    add_executable(lua "${LUA_SOURCES_FOLDER}/lua.c")
    target_link_libraries(lua PUBLIC lua_static)
    set_target_properties(lua PROPERTIES
            RUNTIME_OUTPUT_DIRECTORY ${PROJECT_BINARY_DIR}
            )
    if (HAVE_READLINE_READLINE_H)
        target_compile_definitions(lua PUBLIC "LUA_USE_READLINE")
        target_link_libraries(lua PUBLIC readline)
    endif()
endif()
