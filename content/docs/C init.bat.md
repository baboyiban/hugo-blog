---
title: C init.bat
date: 2025-02-27
time: 16:06
tags: []
---

# C init.bat

```bash
@echo off
setlocal enabledelayedexpansion

echo ==================================
echo C 프로젝트 빌드 시스템 초기화
echo ==================================
echo.

:: 프로젝트 이름 입력받기
set /p PROJECT_NAME="프로젝트 이름을 입력하세요 (기본값: MyProject): "
if "!PROJECT_NAME!"=="" set PROJECT_NAME=MyProject

:: VS Code 폴더 생성
mkdir .vscode 2>nul

:: 필요한 디렉토리 확인 및 생성
echo [1/8] 디렉토리 구조 생성 중...
if not exist src mkdir src
if not exist src\lib mkdir src\lib
if not exist src\main.c (
    echo #include ^<stdio.h^> > src\main.c
    echo. >> src\main.c
    echo int main(void) { >> src\main.c
    echo     printf("Hello, World!\n"); >> src\main.c
    echo     return 0; >> src\main.c
    echo } >> src\main.c
)

:: CMakeLists.txt 생성
echo [2/8] CMakeLists.txt 생성 중...
echo cmake_minimum_required(VERSION 3.15) > CMakeLists.txt
echo project(%PROJECT_NAME% C CXX)  # C와 C++ 모두 지원 >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 빌드 산출물 디렉토리 설정 >> CMakeLists.txt
echo set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) >> CMakeLists.txt
echo set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib) >> CMakeLists.txt
echo set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin) >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 빌드 타입이 지정되지 않은 경우 기본값 설정 >> CMakeLists.txt
echo if(NOT CMAKE_BUILD_TYPE) >> CMakeLists.txt
echo   set(CMAKE_BUILD_TYPE Debug) >> CMakeLists.txt
echo endif() >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 소스 디렉토리 추가 >> CMakeLists.txt
echo include_directories(src) >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # Conan 패키지 찾기 >> CMakeLists.txt
echo find_package(ZLIB CONFIG REQUIRED) >> CMakeLists.txt
echo find_package(GTest CONFIG REQUIRED) >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 모든 C 소스 및 헤더 파일 자동 수집 >> CMakeLists.txt
echo file(GLOB_RECURSE SOURCES "src/*.c") >> CMakeLists.txt
echo file(GLOB_RECURSE HEADERS "src/*.h") >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 수집된 파일 목록 출력 (디버깅용) >> CMakeLists.txt
echo message(STATUS "Found source files: ${SOURCES}") >> CMakeLists.txt
echo message(STATUS "Found header files: ${HEADERS}") >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 실행 파일 생성 >> CMakeLists.txt
echo add_executable(%PROJECT_NAME% ${SOURCES}) >> CMakeLists.txt
echo target_link_libraries(%PROJECT_NAME% PRIVATE ZLIB::ZLIB GTest::gtest GTest::gtest_main) >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 컴파일러 경고 처리 >> CMakeLists.txt
echo if(MSVC) >> CMakeLists.txt
echo     # Windows에서 안전하지 않은 함수 경고 무시 옵션 추가 >> CMakeLists.txt
echo     target_compile_definitions(%PROJECT_NAME% PRIVATE _CRT_SECURE_NO_WARNINGS) >> CMakeLists.txt
echo     # 경고 레벨 설정 >> CMakeLists.txt
echo     target_compile_options(%PROJECT_NAME% PRIVATE /W4) >> CMakeLists.txt
echo else() >> CMakeLists.txt
echo     target_compile_options(%PROJECT_NAME% PRIVATE -Wall -Wextra) >> CMakeLists.txt
echo endif() >> CMakeLists.txt
echo. >> CMakeLists.txt
echo # 소스 그룹 생성 (IDE에서 폴더 구조 유지) >> CMakeLists.txt
echo source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/src PREFIX "Source Files" FILES ${SOURCES}) >> CMakeLists.txt
echo source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/src PREFIX "Header Files" FILES ${HEADERS}) >> CMakeLists.txt

:: conanfile.txt 생성
echo [3/8] conanfile.txt 생성 중...
echo [requires] > conanfile.txt
echo zlib/1.2.11 >> conanfile.txt
echo gtest/1.10.0 >> conanfile.txt
echo. >> conanfile.txt
echo [generators] >> conanfile.txt
echo CMakeDeps >> conanfile.txt
echo CMakeToolchain >> conanfile.txt
echo. >> conanfile.txt
echo [options] >> conanfile.txt
echo zlib/*:shared=False >> conanfile.txt
echo gtest/*:shared=False >> conanfile.txt
echo. >> conanfile.txt
echo [imports] >> conanfile.txt
echo bin, *.dll -^> ./bin >> conanfile.txt
echo lib, *.dylib* -^> ./bin >> conanfile.txt
echo lib, *.so* -^> ./bin >> conanfile.txt

:: build.bat 생성
echo [4/8] build.bat 생성 중...
echo @echo off > build.bat
echo setlocal >> build.bat
echo. >> build.bat
echo :: 빌드 타입 설정 (기본값: Debug) >> build.bat
echo set BUILD_TYPE=Debug >> build.bat
echo if not "%%~1"=="" set BUILD_TYPE=%%~1 >> build.bat
echo echo Building in %%BUILD_TYPE%% mode >> build.bat
echo. >> build.bat
echo :: 빌드 디렉토리 설정 >> build.bat
echo set BUILD_DIR=build >> build.bat
echo. >> build.bat
echo :: 빌드 디렉토리 생성 >> build.bat
echo if not exist %%BUILD_DIR%% mkdir %%BUILD_DIR%% >> build.bat
echo cd %%BUILD_DIR%% >> build.bat
echo. >> build.bat
echo :: Conan 설정 및 종속성 설치 (출력 폴더 명시적 지정) >> build.bat
echo conan install .. --build=missing -s build_type=%%BUILD_TYPE%% -g CMakeDeps -g CMakeToolchain --output-folder=. >> build.bat
echo. >> build.bat
echo :: CMake 구성 (생성된 Conan 툴체인 파일 명시적 지정) >> build.bat
echo cmake .. -DCMAKE_TOOLCHAIN_FILE=conan_toolchain.cmake -DCMAKE_BUILD_TYPE=%%BUILD_TYPE%% >> build.bat
echo. >> build.bat
echo :: 빌드 실행 >> build.bat
echo cmake --build . --config %%BUILD_TYPE%% >> build.bat
echo. >> build.bat
echo :: 실행 파일 경로 출력 >> build.bat
echo echo Build completed. Executable is at: %%CD%%\bin\%%BUILD_TYPE%%\%PROJECT_NAME%.exe >> build.bat
echo. >> build.bat
echo :: 원래 디렉토리로 돌아가기 >> build.bat
echo cd .. >> build.bat
echo. >> build.bat
echo endlocal >> build.bat

:: run.bat 생성
echo [5/8] run.bat 생성 중...
echo @echo off > run.bat
echo setlocal >> run.bat
echo. >> run.bat
echo :: 빌드 타입 설정 (기본값: Debug) >> run.bat
echo set BUILD_TYPE=Debug >> run.bat
echo if not "%%~1"=="" set BUILD_TYPE=%%~1 >> run.bat
echo. >> run.bat
echo :: 실행 파일 경로 >> run.bat
echo set EXECUTABLE_PATH=build\bin\%%BUILD_TYPE%%\%PROJECT_NAME%.exe >> run.bat
echo. >> run.bat
echo :: 실행 파일 존재 확인 >> run.bat
echo if not exist %%EXECUTABLE_PATH%% ( >> run.bat
echo     echo Error: Executable not found at %%EXECUTABLE_PATH%%. >> run.bat
echo     echo Please run build.bat first. >> run.bat
echo     exit /b 1 >> run.bat
echo ) >> run.bat
echo. >> run.bat
echo :: 실행 파일 실행 >> run.bat
echo echo Running %%EXECUTABLE_PATH%%... >> run.bat
echo %%EXECUTABLE_PATH%% %%2 %%3 %%4 %%5 %%6 %%7 %%8 %%9 >> run.bat
echo. >> run.bat
echo endlocal >> run.bat

:: clean.bat 생성
echo [6/8] clean.bat 생성 중...
echo @echo off > clean.bat
echo echo Cleaning build directory... >> clean.bat
echo. >> clean.bat
echo if exist build ( >> clean.bat
echo     rd /s /q build >> clean.bat
echo     echo Build directory removed successfully. >> clean.bat
echo ) else ( >> clean.bat
echo     echo No build directory found. >> clean.bat
echo ) >> clean.bat

:: clean_all.bat 생성
echo [7/8] clean_all.bat 생성 중...
echo @echo off > clean_all.bat
echo echo Performing complete cleanup... >> clean_all.bat
echo. >> clean_all.bat
echo :: 빌드 디렉토리 삭제 >> clean_all.bat
echo if exist build ( >> clean_all.bat
echo     rd /s /q build >> clean_all.bat
echo     echo Build directory removed. >> clean_all.bat
echo ) >> clean_all.bat
echo. >> clean_all.bat
echo :: 프로젝트 루트의 Conan 생성 파일 삭제 >> clean_all.bat
echo if exist conan_toolchain.cmake del /f /q conan_toolchain.cmake >> clean_all.bat
echo if exist conaninfo.txt del /f /q conaninfo.txt >> clean_all.bat
echo if exist conanbuildinfo.* del /f /q conanbuildinfo.* >> clean_all.bat
echo if exist conan.lock del /f /q conan.lock >> clean_all.bat
echo if exist CMakeUserPresets.json del /f /q CMakeUserPresets.json >> clean_all.bat
echo. >> clean_all.bat
echo :: 기타 VS 임시 파일 삭제 >> clean_all.bat
echo if exist .vs rd /s /q .vs >> clean_all.bat
echo if exist *.user del /f /q *.user >> clean_all.bat
echo if exist *.sln del /f /q *.sln >> clean_all.bat
echo if exist *.vcxproj* del /f /q *.vcxproj* >> clean_all.bat
echo. >> clean_all.bat
echo echo Cleanup completed. >> clean_all.bat

:: VS Code 설정 파일 생성
echo [8/8] VS Code 설정 파일 생성 중...

:: tasks.json
echo { > .vscode\tasks.json
echo     "version": "2.0.0", >> .vscode\tasks.json
echo     "tasks": [ >> .vscode\tasks.json
echo         { >> .vscode\tasks.json
echo             "label": "build", >> .vscode\tasks.json
echo             "type": "shell", >> .vscode\tasks.json
echo             "command": "build.bat Debug", >> .vscode\tasks.json
echo             "group": { >> .vscode\tasks.json
echo                 "kind": "build", >> .vscode\tasks.json
echo                 "isDefault": true >> .vscode\tasks.json
echo             }, >> .vscode\tasks.json
echo             "problemMatcher": ["$msCompile"] >> .vscode\tasks.json
echo         }, >> .vscode\tasks.json
echo         { >> .vscode\tasks.json
echo             "label": "build-release", >> .vscode\tasks.json
echo             "type": "shell", >> .vscode\tasks.json
echo             "command": "build.bat Release", >> .vscode\tasks.json
echo             "group": "build", >> .vscode\tasks.json
echo             "problemMatcher": ["$msCompile"] >> .vscode\tasks.json
echo         }, >> .vscode\tasks.json
echo         { >> .vscode\tasks.json
echo             "label": "run", >> .vscode\tasks.json
echo             "type": "shell", >> .vscode\tasks.json
echo             "command": "run.bat", >> .vscode\tasks.json
echo             "dependsOn": ["build"], >> .vscode\tasks.json
echo             "group": { >> .vscode\tasks.json
echo                 "kind": "test", >> .vscode\tasks.json
echo                 "isDefault": true >> .vscode\tasks.json
echo             } >> .vscode\tasks.json
echo         }, >> .vscode\tasks.json
echo         { >> .vscode\tasks.json
echo             "label": "clean", >> .vscode\tasks.json
echo             "type": "shell", >> .vscode\tasks.json
echo             "command": "clean.bat", >> .vscode\tasks.json
echo             "problemMatcher": [] >> .vscode\tasks.json
echo         }, >> .vscode\tasks.json
echo         { >> .vscode\tasks.json
echo             "label": "clean-all", >> .vscode\tasks.json
echo             "type": "shell", >> .vscode\tasks.json
echo             "command": "clean_all.bat", >> .vscode\tasks.json
echo             "problemMatcher": [] >> .vscode\tasks.json
echo         } >> .vscode\tasks.json
echo     ] >> .vscode\tasks.json
echo } >> .vscode\tasks.json

:: launch.json
echo { > .vscode\launch.json
echo     "version": "0.2.0", >> .vscode\launch.json
echo     "configurations": [ >> .vscode\launch.json
echo         { >> .vscode\launch.json
echo             "name": "Debug", >> .vscode\launch.json
echo             "type": "cppvsdbg", >> .vscode\launch.json
echo             "request": "launch", >> .vscode\launch.json
echo             "program": "${workspaceFolder}\\build\\bin\\Debug\\%PROJECT_NAME%.exe", >> .vscode\launch.json
echo             "args": [], >> .vscode\launch.json
echo             "stopAtEntry": false, >> .vscode\launch.json
echo             "cwd": "${workspaceFolder}", >> .vscode\launch.json
echo             "environment": [], >> .vscode\launch.json
echo             "externalConsole": false, >> .vscode\launch.json
echo             "preLaunchTask": "build" >> .vscode\launch.json
echo         } >> .vscode\launch.json
echo     ] >> .vscode\launch.json
echo } >> .vscode\launch.json

:: settings.json
echo { > .vscode\settings.json
echo     "cmake.configureOnOpen": true, >> .vscode\settings.json
echo     "cmake.buildDirectory": "${workspaceFolder}/build", >> .vscode\settings.json
echo     "cmake.defaultVariants": { >> .vscode\settings.json
echo         "buildType": { >> .vscode\settings.json
echo             "default": "debug", >> .vscode\settings.json
echo             "description": "The build type.", >> .vscode\settings.json
echo             "choices": { >> .vscode\settings.json
echo                 "debug": { >> .vscode\settings.json
echo                     "short": "Debug", >> .vscode\settings.json
echo                     "long": "Debug build type", >> .vscode\settings.json
echo                     "buildType": "Debug" >> .vscode\settings.json
echo                 }, >> .vscode\settings.json
echo                 "release": { >> .vscode\settings.json
echo                     "short": "Release", >> .vscode\settings.json
echo                     "long": "Release build type", >> .vscode\settings.json
echo                     "buildType": "Release" >> .vscode\settings.json
echo                 } >> .vscode\settings.json
echo             } >> .vscode\settings.json
echo         } >> .vscode\settings.json
echo     }, >> .vscode\settings.json
echo     "files.associations": { >> .vscode\settings.json
echo         "*.h": "c" >> .vscode\settings.json
echo     }, >> .vscode\settings.json
echo     "cmake.sourceDirectory": "${workspaceFolder}" >> .vscode\settings.json
echo } >> .vscode\settings.json

:: .gitignore 생성
echo # 빌드 디렉토리 > .gitignore
echo /build/ >> .gitignore
echo /bin/ >> .gitignore
echo /lib/ >> .gitignore
echo. >> .gitignore
echo # Visual Studio 파일 >> .gitignore
echo .vs/ >> .gitignore
echo *.user >> .gitignore
echo *.suo >> .gitignore
echo *.sln >> .gitignore
echo *.vcxproj >> .gitignore
echo *.vcxproj.filters >> .gitignore
echo *.vcxproj.user >> .gitignore
echo. >> .gitignore
echo # Conan 파일 >> .gitignore
echo conanbuildinfo.* >> .gitignore
echo conaninfo.txt >> .gitignore
echo conan.lock >> .gitignore
echo conan_toolchain.cmake >> .gitignore
echo CMakeUserPresets.json >> .gitignore
echo. >> .gitignore
echo # 임시 파일 >> .gitignore
echo *.tmp >> .gitignore
echo *.log >> .gitignore

echo.
echo ===============================================
echo 초기화가 완료되었습니다!
echo 프로젝트 이름: %PROJECT_NAME%
echo.
echo 다음 단계:
echo 1. VS Code에서 프로젝트 열기
echo 2. 'build.bat' 실행하여 프로젝트 빌드하기
echo 3. 'run.bat' 실행하여 프로그램 실행하기
echo 4. VS Code에서 F5 키로 디버깅 시작하기
echo ===============================================

endlocal
```