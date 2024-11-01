# Download
```
git clone https://github.com/VTREEM/Carve
```

# Modification
1. Add C++ standard in CMakerLists.txt
    ```
    set(CMAKE_CXX_STANDARD 11)  # or 03
    set(CMAKE_CXX_STANDARD_REQUIRED ON)
    ```

2. `Carve/external/gtest-1.5.0/include/gtest/internal/gtest-port.h:449`
    ```
    // change this 
    // #include <tr1/tuple>  // NOLINT
    // to this
    #include <tuple>  // NOLINT
    ```

3. replace all `std::tr1::tuple` into `std::tuple`
    1. check all files to be replaced 
        `grep -rn "std::tr1::tuple" ../external`
    2.  replace
        `find ../external -type f -name "*.h" -o -name "*.cc" -o -name "*.cpp" | xargs sed -i '' 's/std::tr1::tuple/std::tuple/g'`

4. TODO: deal with `unordered_map`
    1. modify `/Users/wanghsuanchung/Projects/MRDVS/CarveCSG/Carve/include/carve/collection/unordered/boost_impl.hpp`:
        ```c++
        // from 
        // namespace std
        // to 
        namespace std_carve
        ```
    2. check all files with unordered_map but not `std::unordered_map` and `boost::unordered_map`
        - `LC_ALL=C find ../include ../lib ../tests ../common ../src -type f -name "*.h" -o -name "*.h.in" -o -name "*.hpp" -o -name "*.cc" -o -name "*.cpp" | LC_ALL=C xargs sed -i '' 's/std::unordered_map/std_carve::unordered_map/g'`
        - `grep -rn "std::unordered_map" ../`

        - `LC_ALL=C find ../include ../lib ../tests ../common ../src -type f \( -name "*.h" -o -name "*.h.in" -o -name "*.hpp" -o -name "*.cc" -o -name "*.cpp" \) | LC_ALL=C xargs sed -i '' 's/std::unordered_multimap/std_carve::unordered_multimap/g'`
        - `grep -rn "std::unordered_multimap" ../`

        - `LC_ALL=C find ../include ../lib ../tests ../common ../src -type f \( -name "*.h" -o -name "*.h.in" -o -name "*.hpp" -o -name "*.cc" -o -name "*.cpp" \) | LC_ALL=C xargs sed -i '' 's/std::unordered_set/std_carve::unordered_set/g'`
        - `grep -rn "std::unordered_set" ../`

5. `Carve/common/read_ply.cpp:64:45`:
    ```
    // from 
    // virtual void length(size_t len) { if (l > 0) l->curr().second.reserve(len); }
    // to 
    virtual void length(size_t len) { if (l != nullptr) l->curr().second.reserve(len); }
    ```

6. For Installing Carve in `CMakeLists.txt` (Change A Lot, please compare line by line)
```
set(carve_INCLUDE_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/include" "${CMAKE_BINARY_DIR}/include")
include_directories(${carve_INCLUDE_DIRS})
message(STATUS "carve_INCLUDE_DIRS: ${carve_INCLUDE_DIRS}")
```

7. in `/Users/wanghsuanchung/Projects/MRDVS/temp/Carve/include/CMakeLists.txt`
```cmakelist.txt
# change from 
    # PATTERN "*.hpp"
# to
    PATTERN "*.hpp"    
    PATTERN "*.h"
```

# Compile
```
cd build
cmake ..
make
```


# Asking CMakeLists.txt Prompt
```
find ../ -name CMakeLists.txt -exec sh -c 'echo "== {} =="; cat {}' \; > prompt.txt
```

# Remove Care Installation
```
sudo rm -rf /usr/local/lib/libcarve*
sudo rm -rf /usr/local/include/carve
sudo rm -rf /usr/local/lib/cmake/carve
```


# How to includ Carve in your own project (Example CMakeLists.txt)
```
# Minimum CMake version required
cmake_minimum_required(VERSION 3.10)

# Project name and language
project(MachiningSimulation)

# Specify the C++ standard
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set RPATH settings
set(CMAKE_SKIP_BUILD_RPATH FALSE)
set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib:/usr/local/lib")
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

# Locate the carve library and include directories
find_package(Boost REQUIRED)
find_package(carve REQUIRED)

# Specify include directories
include_directories(${Boost_INCLUDE_DIRS})
include_directories(${carve_INCLUDE_DIRS})
# message(STATUS "Boost include directories: ${Boost_INCLUDE_DIRS}")
# message(STATUS "Include directories: ${carve_INCLUDE_DIRS}")

# Add the executable for the main source file
# add_executable(main src/main.cpp)
add_executable(test_carve src/test_carve.cpp)

# Link the carve library
# target_link_libraries(main PRIVATE carve::carve)
target_link_libraries(test_carve PRIVATE carve::carve)

# Optional: Add debugging and optimization flags
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")
```
