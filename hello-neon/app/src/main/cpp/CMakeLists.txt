cmake_minimum_required(VERSION 3.4.1)

add_definitions(-O3 -DNDEBUG)

# build cpufeatures as a static lib
add_library(cpufeatures STATIC
            ${ANDROID_NDK}/sources/android/cpufeatures/cpu-features.c)

# build app's shared lib
if (${ANDROID_ABI} STREQUAL "armeabi-v7a")
  # make a list of neon files and add neon compiling flags to them
  set(neon_SRCS helloneon-intrinsics.c)

  set_property(SOURCE ${neon_SRCS}
               APPEND_STRING PROPERTY COMPILE_FLAGS " -mfpu=neon")
  add_definitions("-DHAVE_NEON=1")
elseif(${ANDROID_ABI} STREQUAL "arm64-v8a")
    set(neon_SRCS helloneon-intrinsics.c)
    add_definitions("-DHAVE_NEON_AARCH64=1")
    add_definitions("-DHAVE_NEON=1")
else ()
  set(neon_SRCS)
endif ()

add_library(hello-neon SHARED
            helloneon.c
            ${neon_SRCS})

target_include_directories(hello-neon PRIVATE
                           ${ANDROID_NDK}/sources/android/cpufeatures)

target_link_libraries(hello-neon
                      android
                      cpufeatures
                      log)

