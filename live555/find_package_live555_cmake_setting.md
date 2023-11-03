
# find_packege live555 cmake setting

## Overview

Live555をDLL化するリポジトリにて、付いてくるcmake用の設定ファイルがあります。  
「live555Config.cmake」という名前で、これを使えばfind_packegeでLive555.dllを探すことが出来ます。  
しかし、cmake 3.24.2ではある行でエラーが発生し、後続処理がストップします。  
その対策を記します。  

## Enviroment

Live555 : 110.0.3  
Cmake : 3.24.2  

## Repository

[https://github.com/melchi45/live555](https://github.com/melchi45/live555)  

## Error message

``` text
CMake Error at </path/to/install>/live555_shared_win64/lib/cmake/live555/live555Config.cmake:110 (set_target_properties):
  Error evaluating generator expression:

    $<TARGET_OBJECTS:EpollTaskScheduler>

  Objects of target "EpollTaskScheduler" referenced but no such target
  exists.
```

## WorkAround

コメントアウトしている箇所が、元々の記述です。  

``` cmake
# Create imported target live555::live555
add_library(live555::live555 SHARED IMPORTED)

set_target_properties(live555::live555 PROPERTIES
  INTERFACE_COMPILE_DEFINITIONS "LIVEMEDIA_API=__declspec(dllimport)"
  INTERFACE_INCLUDE_DIRECTORIES "${_IMPORT_PREFIX}/include/liveMedia"
  #INTERFACE_LINK_LIBRARIES "ws2_32;\$<TARGET_OBJECTS:EpollTaskScheduler>;OpenSSL::SSL;OpenSSL::Crypto"
  INTERFACE_LINK_LIBRARIES "ws2_32;live555::EpollTaskScheduler;OpenSSL::SSL;OpenSSL::Crypto"
)
```

「$<TARGET_OBJECTS:EpollTaskScheduler>」が見つからないので困ってしまいますが、  
その数行前の辺りで「live555::EpollTaskScheduler」を定義していますので、そちらを利用するように記述を変更します。  


