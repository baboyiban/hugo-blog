---
title: Conan으로 C언어 프로젝트 디버그, 컴파일, 실행하는 방법 요약
date: 2025-02-27
time: 15:13
tags: []
---

# Conan으로 C언어 프로젝트 디버그, 컴파일, 실행하는 방법 요약

## 주요 파일 구성

1. **CMakeLists.txt** - 빌드 구성
   - 모든 소스 파일 자동 수집 (`GLOB_RECURSE`)
   - 빌드 결과물 위치 지정 (`CMAKE_RUNTIME_OUTPUT_DIRECTORY`)
   - 외부 라이브러리 설정 (ZLIB, GTest)
   - 컴파일러 경고 설정

2. **conanfile.txt** - 종속성 관리
   - 필요한 외부 라이브러리 지정
   - 빌드 옵션 설정
   - 라이브러리 파일 임포트 방식 지정

3. **빌드 스크립트**
   - `build.bat` - 프로젝트 빌드
   - `run.bat` - 프로그램 실행
   - `clean.bat` - 빌드 파일 정리

4. **VS Code 설정**
   - `tasks.json` - 빌드 작업 자동화
   - `launch.json` - 디버깅 설정

## 주요 명령어

1. **빌드하기**: `build.bat [Debug|Release]`
   - Conan으로 종속성 설치
   - CMake로 빌드 파일 생성
   - 컴파일 및 링크 실행

2. **실행하기**: `run.bat [Debug|Release]`
   - 빌드된 프로그램 실행

3. **정리하기**: `clean.bat`
   - 모든 빌드 파일 삭제

## 디렉토리 구조

```
프로젝트/
│
├── src/               # 소스 코드
│   ├── main.c
│   └── lib/           # 라이브러리 구현
│       ├── Module1/
│       │   ├── Module1.c
│       │   └── Module1.h
│       └── ...
│
├── build/             # 빌드 출력 (자동 생성)
│   ├── bin/           # 실행 파일
│   │   ├── Debug/
│   │   └── Release/
│   └── lib/           # 라이브러리 파일
│
├── CMakeLists.txt     # CMake 설정
├── conanfile.txt      # Conan 종속성
├── build.bat          # 빌드 스크립트
├── run.bat            # 실행 스크립트
└── clean.bat          # 정리 스크립트
```

## 주요 특징

1. **자동화된 소스 관리**
   - 소스 파일 추가 시 CMakeLists.txt 수정 불필요
   - GLOB_RECURSE로 모든 .c 파일 자동 감지

2. **종속성 관리**
   - Conan을 통한 외부 라이브러리 자동 설치
   - 플랫폼 간 일관된 빌드 환경

3. **빌드 결과 정리**
   - 모든 빌드 파일은 build/ 디렉토리에 격리
   - 실행 파일은 build/bin/에 생성

4. **VS Code 통합**
   - F5 키로 디버깅
   - Ctrl+Shift+B로 빌드
   - Tasks로 다양한 작업 실행

## 주의사항

1. 코드 페이지 관련 경고는 소스 파일을 UTF-8로 저장하여 해결
2. CRT 보안 경고는 `_CRT_SECURE_NO_WARNINGS` 정의로 해결
3. 초기 설정 후 소스 파일 추가는 자동으로 감지됨

이 빌드 시스템으로 C 프로젝트를 쉽게 관리하고, 외부 라이브러리 종속성을 효율적으로 처리할 수 있습니다.

아니 내용을 빠트리면 안되고 코드는 다 써줘

# C 프로젝트 빌드 시스템 종합 정리

## 1. CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyProject C CXX)  # C와 C++ 모두 지원

# 빌드 산출물 디렉토리 설정
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

# 빌드 타입이 지정되지 않은 경우 기본값 설정
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Debug)
endif()

# 소스 디렉토리 추가
include_directories(src)

# Conan 패키지 찾기
find_package(ZLIB CONFIG REQUIRED)
find_package(GTest CONFIG REQUIRED)

# 모든 C 소스 및 헤더 파일 자동 수집
file(GLOB_RECURSE SOURCES "src/*.c")
file(GLOB_RECURSE HEADERS "src/*.h")

# 수집된 파일 목록 출력 (디버깅용)
message(STATUS "Found source files: ${SOURCES}")
message(STATUS "Found header files: ${HEADERS}")

# 실행 파일 생성
add_executable(my_program ${SOURCES})
target_link_libraries(my_program PRIVATE ZLIB::ZLIB GTest::gtest GTest::gtest_main)

# 컴파일러 경고 처리
if(MSVC)
    # Windows에서 안전하지 않은 함수 경고 무시 옵션 추가
    target_compile_definitions(my_program PRIVATE _CRT_SECURE_NO_WARNINGS)
    # 경고 레벨 설정
    target_compile_options(my_program PRIVATE /W4)
else()
    target_compile_options(my_program PRIVATE -Wall -Wextra)
endif()

# 소스 그룹 생성 (IDE에서 폴더 구조 유지)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/src PREFIX "Source Files" FILES ${SOURCES})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/src PREFIX "Header Files" FILES ${HEADERS})
```

## 2. conanfile.txt

```
[requires]
zlib/1.2.11
gtest/1.10.0

[generators]
CMakeDeps
CMakeToolchain

[options]
zlib/*:shared=False
gtest/*:shared=False

[imports]
bin, *.dll -> ./build/bin
lib, *.dylib* -> ./build/bin
lib, *.so* -> ./build/bin
```

## 3. build.bat

```batch
@echo off
setlocal

:: 빌드 타입 설정 (기본값: Debug)
set BUILD_TYPE=Debug
if not "%~1"=="" set BUILD_TYPE=%~1
echo Building in %BUILD_TYPE% mode

:: 빌드 디렉토리 설정
set BUILD_DIR=build

:: 빌드 디렉토리 생성
if not exist %BUILD_DIR% mkdir %BUILD_DIR%
cd %BUILD_DIR%

:: Conan 설정 및 종속성 설치 (출력 폴더 명시적 지정)
conan install .. --build=missing -s build_type=%BUILD_TYPE% -g CMakeDeps -g CMakeToolchain --output-folder=.

:: CMake 구성 (생성된 Conan 툴체인 파일 명시적 지정)
cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=%BUILD_TYPE%

:: 빌드 실행
cmake --build . --config %BUILD_TYPE%

:: 실행 파일 경로 출력
echo Build completed. Executable is at: %CD%\bin\%BUILD_TYPE%\my_program.exe

:: 원래 디렉토리로 돌아가기
cd ..

endlocal
```

## 4. run.bat

```batch
@echo off
setlocal

:: 빌드 타입 설정 (기본값: Debug)
set BUILD_TYPE=Debug
if not "%~1"=="" set BUILD_TYPE=%~1

:: 실행 파일 경로
set EXECUTABLE_PATH=build\bin\%BUILD_TYPE%\my_program.exe

:: 실행 파일 존재 확인
if not exist %EXECUTABLE_PATH% (
    echo Error: Executable not found at %EXECUTABLE_PATH%.
    echo Please run build.bat first.
    exit /b 1
)

:: 실행 파일 실행
echo Running %EXECUTABLE_PATH%...
%EXECUTABLE_PATH% %2 %3 %4 %5 %6 %7 %8 %9

endlocal
```

## 5. clean.bat

```batch
@echo off
echo Cleaning build directory...

if exist build (
    rd /s /q build
    echo Build directory removed successfully.
) else (
    echo No build directory found.
)
```

## 6. VSCode 설정 파일 (.vscode/tasks.json)

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "build.bat Debug",
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "problemMatcher": ["$msCompile"]
        },
        {
            "label": "build-release",
            "type": "shell",
            "command": "build.bat Release",
            "group": "build",
            "problemMatcher": ["$msCompile"]
        },
        {
            "label": "run",
            "type": "shell",
            "command": "run.bat",
            "dependsOn": ["build"],
            "group": {
                "kind": "test",
                "isDefault": true
            }
        },
        {
            "label": "clean",
            "type": "shell",
            "command": "clean.bat",
            "problemMatcher": []
        }
    ]
}
```

## 7. VSCode 디버깅 설정 (.vscode/launch.json)

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Debug",
            "type": "cppvsdbg",
            "request": "launch",
            "program": "${workspaceFolder}\\build\\bin\\Debug\\my_program.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "preLaunchTask": "build"
        }
    ]
}
```

## 8. .gitignore

```
# 빌드 디렉토리
/build/
/bin/
/lib/

# Visual Studio 파일
.vs/
*.user
*.suo
*.sln
*.vcxproj
*.vcxproj.filters
*.vcxproj.user

# Conan 파일
conanbuildinfo.*
conaninfo.txt
conan.lock

# 임시 파일
*.tmp
*.log
```

## 9. VSCode 설정 (.vscode/settings.json)

```json
{
    "cmake.configureOnOpen": true,
    "cmake.buildDirectory": "${workspaceFolder}/build",
    "cmake.defaultVariants": {
        "buildType": {
            "default": "debug",
            "description": "The build type.",
            "choices": {
                "debug": {
                    "short": "Debug",
                    "long": "Debug build type",
                    "buildType": "Debug"
                },
                "release": {
                    "short": "Release",
                    "long": "Release build type",
                    "buildType": "Release"
                }
            }
        }
    },
    "files.associations": {
        "*.h": "c"
    }
}
```

## 사용 방법

1. **초기 설정**:
   - 위 파일들을 프로젝트 루트 디렉토리에 생성
   - `.vscode` 폴더에 `tasks.json`, `launch.json`, `settings.json` 파일 생성

2. **빌드하기**:
   - 명령줄에서 `build.bat` 실행
   - VS Code에서 Ctrl+Shift+B 누르기

3. **실행하기**:
   - 명령줄에서 `run.bat` 실행
   - VS Code에서 작업 실행 메뉴를 통해 'run' 작업 선택

4. **디버깅하기**:
   - VS Code에서 F5 키 누르기

5. **정리하기**:
   - 명령줄에서 `clean.bat` 실행
   - VS Code에서 작업 실행 메뉴를 통해 'clean' 작업 선택

이 빌드 시스템은 다음과 같은 이점을 제공합니다:
- 소스 파일을 자동으로 감지하여 빌드합니다.
- Conan을 통해 외부 라이브러리 종속성을 관리합니다.
- 빌드 결과물을 build 디렉토리에 정리합니다.
- VS Code와의 통합으로 편리한 개발 환경을 제공합니다.

문제 해결:
- 코드 페이지 경고는 소스 파일을 UTF-8로 저장하여 해결할 수 있습니다.
- CRT 보안 경고는 `_CRT_SECURE_NO_WARNINGS` 정의를 통해 처리됩니다.