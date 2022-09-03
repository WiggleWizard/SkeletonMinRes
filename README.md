# SkeletonMinRes
SkeletonMinRes allows you to easily embed resources into your C++ project using CMake. Resources can be any file; ranging from textures, GLSL sources, LUA scripts, etc.

# How to use
The main requirement for usage is CMake.

## Cloning
First clone the repo down. Here I like to use submodules with a `thirdparty/<library_name>/repo` type structure to keep things consistent.
```shell
git submodule add https://github.com/WiggleWizard/SkeletonMinRes.git thirdparty/SkeletonMinRes/repo
```
Then add the repo to your CMake file:
```cmake
add_submodule(thirdparty/SkeletonMinRes/repo)
```

## CMake
Now you can add your resources, by calling the provided `embed_resources()` function in your CMakeLists.txt file:
```cmake
embed_resources(PROJECT_RESOURCES
    shaders/vertex.glsl
    shaders/frag.glsl
    scripts/main.lua
    config/engine.ini)
```

Then to add sources and allow the loader to be accessed:
```cmake
add_executable(${PROJECT_NAME} ${SOURCE_FILES} ${PROJECT_RESOURCES})
include_directories(${INCLUDE_DIRS} ${PROJECT_RESOURCES_INCLUDE_DIR})
target_link_libraries(${PROJECT_NAME} SkeletonMinRes)
```

## C++
```cpp
#include <iostream>
using namespace std;

#include <SkeletonMinRes/Loader.h>


int main()
{
    SkeletonMinRes::Resource text = LOAD_RESOURCE(config_engine_ini);
    cout << text.toString() << endl;

    return EXIT_SUCCESS;
}
```

# Credits
Base taken from [CodeFinder2/embed-resource](https://github.com/CodeFinder2/embed-resource).