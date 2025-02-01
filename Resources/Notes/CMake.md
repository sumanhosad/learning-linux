
### **1. What is CMake?**
CMake is a cross-platform build system generator. Unlike `make`, which directly processes `Makefiles`, CMake generates `Makefiles` (or project files for other build systems like Ninja, Visual Studio, etc.) and then you use those generated files to build your project.

### **2. Basic CMake Workflow**
1. Write a `CMakeLists.txt` file (this defines how your project is built).
2. Run `cmake` to generate build files.
3. Use `make` (or another build tool) to compile your code.

---

## **Writing a Simple CMakeLists.txt**
Let's say you have a simple project:

```
project_root/
│── src/
│   ├── main.cpp
│   ├── foo.cpp
│   ├── foo.h
│── CMakeLists.txt
│── build/  (Generated build files go here)
```

### **Basic CMakeLists.txt**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 17)  # Use C++17

add_executable(my_executable src/main.cpp src/foo.cpp)
```
This does the following:
- Sets the project name.
- Defines C++ standard version.
- Creates an executable from `main.cpp` and `foo.cpp`.

### **Building with CMake**
Run the following commands:
```sh
mkdir build
cd build
cmake ..
make
```
This will generate the `my_executable` binary inside the `build/` directory.

---

## **3. Including Directories (`include_directories`)**
If your header files are inside an `include/` directory, you must tell CMake where to find them.

### Example:
```
project_root/
│── src/
│   ├── main.cpp
│   ├── foo.cpp
│── include/
│   ├── foo.h
│── CMakeLists.txt
│── build/
```

Modify `CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 17)

include_directories(include)  # Add include directory

add_executable(my_executable src/main.cpp src/foo.cpp)
```

---

## **4. Linking External Libraries (`find_package` & `target_link_libraries`)**
If you're using external libraries (like Boost or OpenCV), you need to find and link them.

### **Example: Linking Boost**
```cmake
find_package(Boost REQUIRED COMPONENTS filesystem system)
include_directories(${Boost_INCLUDE_DIRS})

add_executable(my_executable src/main.cpp src/foo.cpp)
target_link_libraries(my_executable ${Boost_LIBRARIES})
```

---

## **5. Organizing Large Projects with Subdirectories**
If your project grows, you might want to split it into multiple subdirectories.

```
project_root/
│── src/
│   ├── main.cpp
│   ├── foo.cpp
│── include/
│   ├── foo.h
│── lib/
│   ├── CMakeLists.txt  # Separate for a library
│   ├── mylib.cpp
│   ├── mylib.h
│── CMakeLists.txt
│── build/
```

Modify the **top-level** `CMakeLists.txt`:
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

set(CMAKE_CXX_STANDARD 17)

add_subdirectory(lib)  # Include the library
include_directories(include)

add_executable(my_executable src/main.cpp src/foo.cpp)
target_link_libraries(my_executable mylib)  # Link the library
```

And the `lib/CMakeLists.txt`:
```cmake
add_library(mylib mylib.cpp)
```

---

### **6. Using CMake Presets (Optional)**
Instead of always running `cmake ..` manually, you can use `CMakePresets.json` to define build options.

---

### **7. Using CMake with Ninja (Faster Builds)**
Instead of `make`, you can use Ninja for faster builds:
```sh
cmake -G Ninja ..
ninja
```

---
