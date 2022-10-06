
# Ninja Build

## Introduction

    CMakeとNinjaでビルドする手順。  
    
## Environment

    OS: Windows 10  
    コンパイラ: LLVM(15.0.0+)  

## 事前準備

    1. LLVMをインストールし、pathを通します。  
[release_llvm](https://releases.llvm.org/)  

    2. Ninjaをインストールし、pathを通します。  
[ninja-build](https://ninja-build.org/)  

    3. CMakeをインストールし、Pathを通します。  
[CMake](https://cmake.org/)  

## Command

``` shell
powershell.exe cmake -G Ninja -S . -B build  
powershell.exe cmake --build build
```

