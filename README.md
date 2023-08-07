# Lua CMake <img src="https://github.com/Sororfortuna/LuaCMake/assets/18470725/b54c0282-5399-453c-9d82-90af5e8c688f" alt="icon" width="128" height="128" align="left" valign="middle">
Keeping updated with the latest Lua source code with github actions, updated automatically every week.

![example workflow](https://github.com/Sororfortuna/LuaCMake/actions/workflows/update.yml/badge.svg)

```
$ git submodule add https://github.com/Sororfortuna/LuaCMake Lua
```
```
$ git submodule update --init --recursive --progress
```
```cmake
add_subdirectory(Lua)
target_link_libraries(${PROJECT_NAME} PRIVATE lua::static)
```
<sub>Forked from [Kepler-Br/LuaCMake](https://github.com/Kepler-Br/LuaCMake)</sub>
