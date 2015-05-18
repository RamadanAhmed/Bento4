cmake_minimum_required(VERSION 3.0)

# Variables
set(SOURCE_ROOT Source/C++)
set(SOURCE_CODECS ${SOURCE_ROOT}/Codecs)
set(SOURCE_CORE ${SOURCE_ROOT}/Core)
set(SOURCE_CRYPTO ${SOURCE_ROOT}/Crypto)
set(SOURCE_METADATA ${SOURCE_ROOT}/MetaData)
set(SOURCE_SYSTEM ${SOURCE_ROOT}/System)

# AP4 Library
file(GLOB AP4_SOURCES
  ${SOURCE_CODECS}/*.cpp
  ${SOURCE_CORE}/*.cpp
  ${SOURCE_CRYPTO}/*.cpp
  ${SOURCE_METADATA}/*.cpp
  ${SOURCE_SYSTEM}/StdC/*.cpp
)

if(WIN32)
  set(AP4_SOURCES ${AP4_SOURCES} ${SOURCE_SYSTEM}/Win32/Ap4Win32Random.cpp)
else()
  set(AP4_SOURCES ${AP4_SOURCES} ${SOURCE_SYSTEM}/Posix/Ap4PosixRandom.cpp)
endif()

add_library(ap4 STATIC ${AP4_SOURCES})

# Includes
include_directories(
  ${SOURCE_CORE}
  ${SOURCE_CODECS}
  ${SOURCE_CRYPTO}
  ${SOURCE_METADATA}
)

# Compiler warning and optimization flags
## cmake -DCMAKE_BUILD_TYPE=Debug ..
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -Wextra")
## cmake -DCMAKE_BUILD_TYPE=Release ..
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -Wall -Wextra -O3")

# Apps
set(BENTO4_APPS "Aac2Mp4;Mp42Aac;Mp42Ts;Mp42Hls;Mp4Compact;Mp4DcfPackager;Mp4Decrypt;Mp4Dump;Mp4Edit;Mp4Encrypt;Mp4Extract;Mp4Fragment;Mp4Info;Mp4Mux;Mp4Split;Mp4Tag")
foreach(app ${BENTO4_APPS})
  string(TOLOWER ${app} binary_name)
  add_executable(${binary_name} ${SOURCE_ROOT}/Apps/${app}/${app}.cpp)
  target_link_libraries(${binary_name} ap4)
endforeach()