### 🚀 **XMake: Rust의 Cargo 같은 C 빌드 시스템 사용법**

XMake는 **C, C++ 프로젝트에서 Cargo처럼 간편한 빌드 + 실행 + 패키지 관리**를 제공합니다.  
Rust에서 `cargo build`, `cargo run`을 쓰듯이, XMake에서는 `xmake`, `xmake run`을 사용할 수 있습니다.

---

## **1️⃣ XMake 설치**

📌 **Linux/macOS**

```sh
curl -fsSL https://xmake.io/shget.text | bash
```

📌 **Windows**  
PowerShell에서 실행:

```powershell
iwr -useb https://xmake.io/psget.text | iex
```

📌 **또는 패키지 매니저 사용**

- macOS: `brew install xmake`
- Linux: `sudo apt install xmake` (Ubuntu), `sudo pacman -S xmake` (Arch)
- Windows: `scoop install xmake` 또는 `choco install xmake`

설치 확인:

```sh
xmake --version
```

---

## **2️⃣ 새로운 프로젝트 생성**

📌 **C 프로젝트 생성**

```sh
xmake create -l c myproject
cd myproject
```

📌 **C++ 프로젝트 생성**

```sh
xmake create -l c++ myproject
cd myproject
```

---

## **3️⃣ 프로젝트 구조**

생성된 프로젝트 폴더는 아래처럼 구성됩니다.

```
myproject/
├── src/
│   ├── main.c  # 메인 코드
├── xmake.lua   # 빌드 설정 파일 (Cargo.toml 같은 역할)
```

👉 `xmake.lua` 파일이 **Cargo.toml** 같은 역할을 합니다.

---

## **4️⃣ 빌드 & 실행**

📌 **프로젝트 빌드**

```sh
xmake
```

📌 **프로그램 실행**

```sh
xmake run
```

📌 **디버그 빌드**

```sh
xmake f -m debug   # Debug 모드 설정
xmake              # 다시 빌드
xmake run          # 실행
```

📌 **릴리즈 빌드**

```sh
xmake f -m release   # Release 모드 설정
xmake                # 빌드
```

---

## **5️⃣ 코드 수정 (xmake.lua 설정)**

기본적으로 `xmake.lua`는 다음과 같이 생성됩니다.

```lua
add_rules("mode.debug", "mode.release")

target("myproject")
    set_kind("binary")
    add_files("src/*.c")
```

- `add_rules("mode.debug", "mode.release")` → 디버그 & 릴리즈 모드 지원
- `target("myproject")` → 실행 파일 이름
- `set_kind("binary")` → 실행 가능한 프로그램으로 빌드
- `add_files("src/*.c")` → `src/` 폴더 안의 `.c` 파일을 포함

📌 **C++ 프로젝트로 변경하려면?**

```lua
target("myproject")
    set_kind("binary")
    set_languages("c++17")   -- C++17 설정
    add_files("src/*.cpp")
```

---

## **6️⃣ 패키지(라이브러리) 관리**

Rust의 Cargo처럼 라이브러리를 쉽게 추가할 수 있습니다.  
📌 **예제: OpenSSL 추가**

```lua
add_requires("openssl")
target("myproject")
    set_kind("binary")
    add_files("src/*.c")
    add_packages("openssl")
```

📌 **패키지 설치**

```sh
xmake require --info openssl
```

📌 **패키지 목록 검색**

```sh
xmake repo --search zlib
```

---

## **7️⃣ 디버깅**

📌 **GDB/Lldb 디버깅**

```sh
xmake run -d
```

📌 **VScode와 연동**

```sh
xmake project -k vsxmake
```

📌 **CMake 프로젝트 변환**

```sh
xmake project -k cmake
```

---

## **🎯 XMake 사용 요약**

|기능|명령어|
|---|---|
|프로젝트 생성|`xmake create -l c myproject`|
|빌드|`xmake`|
|실행|`xmake run`|
|디버그 실행|`xmake run -d`|
|릴리즈 빌드|`xmake f -m release && xmake`|
|CMake 프로젝트로 변환|`xmake project -k cmake`|
|VSCode 프로젝트로 변환|`xmake project -k vsxmake`|
|패키지 설치|`xmake require --info <package>`|

---

## **🚀 결론: Cargo 같은 XMake!**

✔ **단순한 명령어 (`xmake`, `xmake run`)**  
✔ **C/C++ 패키지 관리 (`add_requires("openssl")`)**  
✔ **디버깅 (`xmake run -d`) & 크로스 플랫폼 지원**

👉 **Rust의 Cargo처럼 쉽게 C 프로젝트를 관리하려면 XMake가 최고의 선택!**  
필요한 추가 설정이 있으면 알려주세요! 😊