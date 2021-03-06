project(MaterialX)
set(MATERIALX_MAJOR_VERSION 1)
set(MATERIALX_MINOR_VERSION 35)
set(MATERIALX_BUILD_VERSION 3)

cmake_minimum_required(VERSION 3.1)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_POSITION_INDEPENDENT_CODE TRUE)
set(CMAKE_MACOSX_RPATH ON)
enable_testing()

option(MATERIALX_BUILD_PYTHON "Build the MaterialX Python package from C++ bindings. Requires Python 2.6 or greater." OFF)
option(MATERIALX_GENERATE_DOCS "Create HTML documentation using Doxygen. Requires that Doxygen be installed." OFF)

mark_as_advanced(MATERIALX_GENERATE_DOCS)

set(MATERIALX_PYTHON_EXECUTABLE "" CACHE FILEPATH
    "Path to the Python executable (e.g. 'C:/Python27/python.exe').")
set(MATERIALX_PYTHON_INCLUDE_DIR "" CACHE PATH 
    "Path to the headers of the Python installation (e.g. 'C:/Python27/include').")
set(MATERIALX_PYTHON_LIBRARY "" CACHE FILEPATH
    "Path to the Python library file (e.g. 'C:/Python27/libs/python27.lib').")
set(MATERIALX_PYTHON_OCIO_DIR "" CACHE PATH
    "Path to a folder containing the default OCIO configuration to be packaged with MaterialX Python (e.g. 'D:/Projects/OpenColorIO-Configs/aces_1.0.3').")

mark_as_advanced(MATERIALX_PYTHON_EXECUTABLE)
mark_as_advanced(MATERIALX_PYTHON_INCLUDE_DIR)
mark_as_advanced(MATERIALX_PYTHON_LIBRARY)
mark_as_advanced(MATERIALX_PYTHON_OCIO_DIR)

set(PYTHON_INCLUDE_DIR ${MATERIALX_PYTHON_INCLUDE_DIR})
set(PYTHON_LIBRARY ${MATERIALX_PYTHON_LIBRARY})

# Adjust the default installation path
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/installed" CACHE PATH "Default install path" FORCE)
endif()

# Adjust compiler settings
if(MSVC)
    if(CMAKE_CXX_FLAGS MATCHES "/W[0-4]")
        string(REGEX REPLACE "/W[0-4]" "/W4" CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
    else()
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
    endif()
elseif(CMAKE_COMPILER_IS_GNUCXX)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
endif()

# Add subdirectories
add_subdirectory(source/MaterialXCore)
add_subdirectory(source/MaterialXFormat)
add_subdirectory(source/MaterialXTest)

if(MATERIALX_BUILD_PYTHON)
    add_subdirectory(source/PyMaterialX)
    add_subdirectory(python)
endif(MATERIALX_BUILD_PYTHON)

if(MATERIALX_GENERATE_DOCS)
    add_subdirectory(documents)
endif(MATERIALX_GENERATE_DOCS)
