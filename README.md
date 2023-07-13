# Lua CMake
Keeping updated with the latest Lua source code with github actions, updated automatically every day if any changes.

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
