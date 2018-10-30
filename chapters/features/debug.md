# Debugger support

## Building in debug mode

For single-configuration generators, you can build your code with `-DCMAKE_BUILD_TYPE=Debug` to get debugging flags. In multi-configuration generators, like many IDEs, you can pick the configuration in the IDE. There are distinct flags for this mode (variables ending in `_DEBUG` as opposed to `_RELEASE`), as well as a generator expression value `CONFIG:Debug` or `CONFIG:Release`.

