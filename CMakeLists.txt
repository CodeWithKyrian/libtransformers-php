cmake_minimum_required(VERSION 3.10)
project(LibTransformersPHP VERSION 0.5.0)

# Set C standard
set(CMAKE_C_STANDARD 11)

# Include directory for header files
include_directories(include)

# Dynamically find all source files in the src directory
file(GLOB SOURCES "src/*.c")

# Dynamically find all header files in the include directory
file(GLOB HEADERS "include/*.h")

# Add KISS FFT source files
set(KISSFFT_SRCS kiss/kiss_fft.c  kiss/kiss_fftr.c)

# Create shared library
add_library(libtransformers_php SHARED ${SOURCES} ${KISSFFT_SRCS})

# Conditional linking for math library
if (UNIX)
    target_link_libraries(libtransformers_php m)
endif()

# Set library version
set_target_properties(libtransformers_php PROPERTIES VERSION ${PROJECT_VERSION})

# Set 
if (WIN32)
    target_compile_definitions(libtransformers_php PRIVATE FAST_TRANSFORMERS_UTILS_EXPORTS)
endif()

# Install library
install(TARGETS libtransformers_php DESTINATION lib)

# Detect the platform and architecture
if (WIN32)
    set(PLATFORM_NAME "windows")
    if (CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(ARCHITECTURE "x86_64")
    else()
        set(ARCHITECTURE "x86")
    endif()
elseif (APPLE)
    set(PLATFORM_NAME "macos")
    if (CMAKE_OSX_ARCHITECTURES STREQUAL "arm64")
        set(ARCHITECTURE "arm64")
    else()
        set(ARCHITECTURE "x86_64")
    endif()
elseif (UNIX)
    set(PLATFORM_NAME "linux")
    if (CMAKE_SYSTEM_PROCESSOR STREQUAL "aarch64")
        set(ARCHITECTURE "aarch64")
    else()
        set(ARCHITECTURE "x86_64")
    endif()
endif()

# Install header file
install(FILES include/transformers_php.h DESTINATION include)

# Configure CPack
set(CPACK_PACKAGE_NAME "libtransformers_php")
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_PACKAGE_CONTACT "kyrianobikwelu@gmail.com")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Fast Utilities for TransformersPHP")
set(CPACK_PACKAGE_VENDOR "CodeWithKyrian")
set(CPACK_PACKAGE_LICENSE "MIT")
set(CPACK_PACKAGE_HOMEPAGE_URL "https://codewithkyrian.com")
set(CPACK_PACKAGE_FILE_NAME "${CPACK_PACKAGE_NAME}-${CPACK_PACKAGE_VERSION}-${PLATFORM_NAME}-${ARCHITECTURE}")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/LICENSE")

# Specify package output directory
set(CPACK_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/releases")

# Define the package type(s) you want to create
set(CPACK_GENERATOR "TGZ")

# Include CPack
include(CPack)