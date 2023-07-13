cmake_minimum_required(VERSION 3.7...3.20)

if (${CMAKE_VERSION} VERSION_LESS 3.12)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
endif ()

if (CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
    message(FATAL_ERROR "Do not build in-source. Please remove CMakeCache.txt and the CMakeFiles/ directory. Then build out-of-source.")
endif ()

option(LUA_BUILD_SHARED "Build lua shared library" ON)
option(LUA_BUILD_STATIC "Build lua static library" ON)

project(LuaCMake
        LANGUAGES C
        DESCRIPTION "Lua embeddable scripting language"
)

set(LUA_SOURCES
    "lua/lapi.c"
    "lua/lcode.c"
    "lua/lctype.c"
    "lua/ldebug.c"
    "lua/ldo.c"
    "lua/ldump.c"
    "lua/lfunc.c"
    "lua/lgc.c"
    "lua/llex.c"
    "lua/lmem.c"
    "lua/lobject.c"
    "lua/lopcodes.c"
    "lua/lparser.c"
    "lua/lstate.c"
    "lua/lstring.c"
    "lua/ltable.c"
    "lua/ltm.c"
    "lua/lundump.c"
    "lua/lvm.c"
    "lua/lzio.c"
    "lua/lauxlib.c"
    "lua/lbaselib.c"
    "lua/lcorolib.c"
    "lua/ldblib.c"
    "lua/liolib.c"
    "lua/lmathlib.c"
    "lua/loadlib.c"
    "lua/loslib.c"
    "lua/lstrlib.c"
    "lua/ltablib.c"
    "lua/lutf8lib.c"
    "lua/linit.c"
)

message(STATUS "Building lua version ${PROJECT_VERSION}")

if (LUA_BUILD_STATIC)
    add_library(lua_static STATIC ${LUA_SOURCES})
    target_include_directories(lua_static
            PUBLIC
            ${LUA_SOURCES_FOLDER}
            )
    add_library(lua::static ALIAS lua_static)

    if (UNIX)
        set(LUA_DEFINITIONS)

        if (NOT EMSCRIPTEN)
            find_library(LIBM m)
            if (NOT LIBM)
                message(FATAL_ERROR "libm not found and is required by lua")
            endif ()
            target_link_libraries(lua_static INTERFACE ${LIBM})

            list(APPEND LUA_DEFINITIONS LUA_USE_POSIX)
            if (LUA_SUPPORT_DL)
                target_compile_definitions(lua_static PRIVATE "LUA_USE_DLOPEN")
                target_link_libraries(lua_static INTERFACE dl)
            endif ()
        endif ()

        target_compile_definitions(lua_static
                PUBLIC ${LUA_DEFINITIONS}
                )
        target_compile_options(lua_static
                PRIVATE "-Wall" "-Wextra"
                )
    endif ()
endif ()

if (LUA_BUILD_SHARED)
    add_library(lua_shared SHARED ${LUA_SOURCES})
    target_include_directories(lua_shared PUBLIC lua)
    add_library(lua::shared ALIAS lua_shared)
endif ()