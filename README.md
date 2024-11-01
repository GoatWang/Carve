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

# Compile
```
cd build
cmake ..
make
```
