---
title: CMakeLists.txt
date: 2025-05-15
time: 11:29
tags: []
---

# CMakeLists.txt

```CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(MyProject)

file(GLOB_RECURSE SOURCES
  "src/*.cpp"
  "src/*.c"
  "src/*.h"
)

add_executable(${PROJECT_NAME} ${SOURCES})

include_directories(
  src/header
)

target_link_libraries(${PROJECT_NAME} m)
```
