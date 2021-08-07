# Lua but it's all CMake

Shout-out to [Walter Schell](https://github.com/walterschell) and Walter
Schell's [repo](https://github.com/walterschell/Lua).  
Main reason behind this repo is to make new CMake file based on Walter Schell's one, but with shared library build option.

## Why just not to fork?

Because [BLOB](https://github.com/walterschell/Lua/blob/1b44ef82ee7a2abf7355052f2aad49e44008778e/orig_sources/lua-5.4.3.tar.gz)
in the repository is not what makes me happy.  
Plus I use git submodule feature and official [repo](https://github.com/lua/lua/tree/master) as submodule.  
And, as I said before, to make shared library build possible.

## What version is this?

It's 5.4.4.

## Have you tested it?

Not much. Ubuntu 21.04 works. MINGW and MacOS *might* work.  
Visual studio...

## CMake flags

| Flag                 | Meaning                                                        | Default       |
| -------------        | -------------                                                  | ------------- |
| `-DLUA_BUILD_BINARY` | Build `lua` interpreter. Builds static library as a dependency | OFF           |
| `-DLUA_BUILD_SHARED` | Build shared library                                           | ON            |
| `-DLUA_BUILD_STATIC` | Build static library                                           | ON            |
| `-DLUA_BUILD_AS_CXX` | Build library as C++                                           | ON            |

## How to submodule me

```shell
git submodule add https://github.com/Kepler-Br/LuaCMake Lua
```

And then

```shell
git submodule update --init --recursive --progress
```

## How to include me into CMake project

```cmake
add_subdirectory(Lua)

# Shared version
target_link_libraries(<YOURTARGET> lua::shared)
# Static version
target_link_libraries(<YOURTARGET> lua::static)
```
