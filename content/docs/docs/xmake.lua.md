---
title: xmake.lua
description: xmake.lua
---

```lua
-- 프로젝트 기본 설정
set_project("MyProject")
set_version("0.0.0")
set_languages("c99")

-- 공통 라이브러리 (코드 중복 방지)
target("common_lib")
set_kind("static")
add_files("include/*/*.c")
add_includedirs("include")
add_headerfiles("include/*/*.h")

-- 메인 애플리케이션
target("main")
set_kind("binary")
add_files("src/*.c")
add_includedirs("src", "include")
add_deps("common_lib")     -- 공통 라이브러리 의존성 추가

-- 테스트 타겟
target("test")
set_kind("binary")
add_files("tests/*.c")
add_includedirs("tests", "include")
add_deps("common_lib")     -- 공통 라이브러리 의존성 추가
add_defines("TESTING")     -- 테스트 전용 매크로

-- 필요한 경우에만 특별한 실행 설정 추가
-- on_run(function(target)
--     os.execv(target:targetfile(), {"--verbose"})  -- 예: 특별한 인자 전달
-- end)
```