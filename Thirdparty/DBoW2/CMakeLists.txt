cmake_minimum_required(VERSION 2.8...3.5)
project(DBoW2)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

add_compile_options(-w)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)


# --- Set Global Compiler Flags ---
set(BASE_CXX_FLAGS "-Wall -D_LINUX -D_REENTRANT -Wno-unused-variable -Wno-maybe-uninitialized -Wno-deprecated-declarations")
if(CMAKE_SYSTEM_PROCESSOR MATCHES "aarch64|armv8")
  set(ARCH_FLAGS "-O3 -march=armv8-a")
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "arm")
  set(ARCH_FLAGS "-O3 -mfpu=neon -march=armv7-a")
else() # x86_64
  set(ARCH_FLAGS "-O3 -mmmx -msse -msse2 -msse3 -mssse3")
endif()

# Append these flags to the default CMake flags
set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} ${BASE_CXX_FLAGS} ${ARCH_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${BASE_CXX_FLAGS} ${ARCH_FLAGS}")

set(HDRS_DBOW2
  DBoW2/BowVector.h
  DBoW2/FORB.h 
  DBoW2/FClass.h       
  DBoW2/FeatureVector.h
  DBoW2/ScoringObject.h   
  DBoW2/TemplatedVocabulary.h)
set(SRCS_DBOW2
  DBoW2/BowVector.cpp
  DBoW2/FORB.cpp      
  DBoW2/FeatureVector.cpp
  DBoW2/ScoringObject.cpp)

set(HDRS_DUTILS
  DUtils/Random.h
  DUtils/Timestamp.h)
set(SRCS_DUTILS
  DUtils/Random.cpp
  DUtils/Timestamp.cpp)


find_package(OpenCV 4 QUIET)
if(NOT OpenCV_FOUND)
   find_package(OpenCV 3.0 QUIET)
   if(NOT OpenCV_FOUND)
      message(FATAL_ERROR "OpenCV > 3.0 not found.")
   endif()
endif()

set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

include_directories(${OpenCV_INCLUDE_DIRS})
add_library(DBoW2 SHARED ${SRCS_DBOW2} ${SRCS_DUTILS})
target_link_libraries(DBoW2 ${OpenCV_LIBS})

