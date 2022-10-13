
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

    3. CMakeをインストールし、pathを通します。  
[CMake](https://cmake.org/)  

## Command

``` shell
powershell.exe cmake -G Ninja -S . -B build  
powershell.exe cmake --build build
```

## Debug, Release ビルドの作り方

    CMake + Ninjaでビルドする場合、Debug, Releaseビルドを指定するコンフィグを作成するには以下のコマンドを利用します。  

``` shell
powershell.exe cmake -S . -B build -G "\"Ninja Multi-Config\""
```

    NinjaはMulti-Configの生成にも対応しています。  
    Multi-Configで設定ファイルを作成した場合、生成されるファイルは以下画像のようになります。  

![ninja_build](https://user-images.githubusercontent.com/12496951/195542757-4c72a5ee-f24c-4778-aa5d-4488562bd817.PNG)  

    あとはビルドするだけです。  
    ex. Debug Build
``` shell
powershell.exe cmake --build build --config debug
```

    ex. Release Build
``` shell
powershell.exe cmake --build build --config release
```

    OpenALをビルドしてみると、以下の違いがありました。  
    - Debug Build  
![debug](https://user-images.githubusercontent.com/12496951/195544079-643e0c4f-e46e-4892-8dab-263031d4a4e1.PNG)  

    - Release Build  
![release](https://user-images.githubusercontent.com/12496951/195544229-620b3d73-6e44-4ec1-b44a-ea7aacacbfb7.PNG)  

    「どの部分に差があるのか」は調べていませんが、1項目だけ差があるようです。  
    生成された「OpenAL32.dll」自体もDebugビルドは2.3MB以上ありますが、Releaseビルドは1.3MB程度です。  
    容量削減や高速化を考えると、Releaseビルドも作るようにしたほうが良さそうです。  

### ref
[CMake Ninja Multi-Config generator](https://www.scivision.dev/cmake-ninja-multi-config/)

