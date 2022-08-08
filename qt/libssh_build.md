
# libssh build

## Introduction

    libsshをAndroidで使うため、自前でビルドが必要になりました。  
    結果的に使えるようになったっぽいので手順をまとめておきます。  
    ちなみに、Qt for Androidを用いた開発は初でしたので諸所の不備はご容赦ください。  

## Environment

    Qt: 5.15.2(2022/08/08 LGPL ver latest)  
    libssh: 0.9.6  
    OpenSSL: 1.1.1l(build by KDAB)  
    CMake: 3.22.1  
    Ninja: 1.11.0  
    OS: Windows 10 pro(19044.1826)  

## Warning

    この手順では、Qt for Androidでlibsshを使うことを目指しています。  
    そのため、一部のパッケージ(OpenSSL)はQtに関連したものを使用していますのでご注意ください。  
    また、レアケースかもしれませんが、OSはWindows 10を使用しています。  
    加えて、CMake + Ninjaを使用しています。  
    Windowsの環境変数(PATH)にCMakeとNinjaを追加しておいてください。  

## Preparation

### 環境変数(PATH)の設定
    
    CMakeとNinjaはすでにダウンロードし、環境変数(PATH)に追加されているものとして話を進めます。  
    それぞれダウンロードして、PATHにフォルダパスを追加して、cmake --version とか ninja --version とか実行してバージョンが出たらOKだと思います。  

### OpenSSLの準備  

    libsshをビルドするためには、sslのパッケージが必要になります。  
    この手順では、OpenSSLを用いてビルドを行います。  
    OpenSSLはQtで容易にサポートが出来るようにKDABさんがビルドして公開してくれているリポジトリがあります。  
    依存関係の解決にはこちらのリポジトリを使用します。  
(Android OpenSSL support for Qt)[https://github.com/KDAB/android_openssl]  

    こちらのリポジトリは「Qt Creator」をインストールし、Qt for Androidの設定を行った際にダウンロード・インストールされるようです。  
    Qt for Androidセットアップ時に以下のような設定を行ったと思います。  
    1. Qt Creator Menu > Edit > Preferrence > Devices へアクセスする。  
    2. Android OpenSSL settings (Optional) でエラーが出ているので[Download OpenSSL]などでダウンロードを行う。  
    3. [OpenSSL Settings are OK.]が出ていることを確認する。  

    この時、Qt Creator は上述リポジトリから対象のフォルダへビルド済みのOpenSSLをダウンロードするのだと思います。  
    もし、まだOpenSSLがダウンロードされていなかったりエラーが解消されていない場合、こちらを先に解消してください。  

## Step of build

    準備は終わりましたので、libsshをビルドしていきます。  

    1. libsshのダウンロード  
       以下のURLよりlibsshのソースコードをダウンロードします。  
(libssh)[https://www.libssh.org/]  

       公式サイトの「download」からダウンロードします。  
       「Index of files」をたどり、0.9.6 をダウンロードします。  
       (2022/08/08 時点ではlibssh-0.9.6.tar.xzになっています)  
       
    2. 任意の場所で解凍
       ダウンロードしてきたら、任意の場所で解凍します。  
       私は7zip野郎なので7zipで解凍します。  
       以下っぽい感じのディレクトリ構成になっていると思います。  

    3. ビルド
       今回はCMakeLists.txtは作らず、己が力でビルドします。  
       libssh-0.9.6フォルダ内でコマンドプロンプトを起動し、buildフォルダを作成後、移動します。  
       
       
       要所要所をご自身の環境に合わせて設定し、実行します。  

``` cmake
cmake -DANDROID_ABI=armeabi-v7a ^
      -DANDROID_NATIVE_API_LEVEL=16 ^
      -DCMAKE_TOOLCHAIN_FILE=C:\Users\<UserName>\AppData\Local\Android\Sdk\ndk\21.4.7075529\build\cmake\android.toolchain.cmake ^
      -DWITH_SERVER=OFF ^
      -DWITH_GSSAPI=OFF ^
      -DWITH_EXAMPLES=OFF ^
      -DCMAKE_BUILD_TYPE=Release ^
      -DOPENSSL_ROOT_DIR="C:\Users\<UserName>\AppData\Local\Android\Sdk\android_openssl\static" ^
      -DOPENSSL_INCLUDE_DIR="C:\Users\<UserName>\AppData\Local\Android\Sdk\android_openssl\static\include" ^
      -DOPENSSL_CRYPTO_LIBRARY="C:\Users\<UserName>\AppData\Local\Android\Sdk\android_openssl\static\lib\multiabi\libcrypto_armeabi-v7a.a" ^
      -DCMAKE_INSTALL_PREFIX="c:\libssh-0.9.6\armeabi-v7a" ^
      -GNinja ..
```

      <UserName> はご自身の環境に依存します。  
      また、ndkのバージョンもインストール済みのバージョンをご使用ください。  

      <OPENSSL_ROOT_DIR>, <OPENSSL_INCLUDE_DIR>, <OPENSSL_CRYPTO_LIBRARY>は設定しないとビルドがそもそも実行出来ないようです。  
      始めは「<OPENSSL_ROOT_DIR>を設定せよ」と怒られたので設定しましたがそれでも失敗していました。  
      調べた結果、<OPENSSL_INCLUDE_DIR>, <OPENSSL_CRYPTO_LIBRARY>を設定することで解決しました。  

      <OPENSSL_CRYPTO_LIBRARY>ですが、android_opensslにはlibの中に「multiabi」と「arm」「arm64」など複数のフォルダと同じようなsoファイルがあります。  
      正直、どれを使えば良いのかはよくわかっていませんが、Android端末の環境に合っていればビルドも実行もOKのようです。  
      winmergeでバイナリ比較してみると、「ファイルは同一です」と出るのでたぶん開発者の考え方次第だと思います。  
      
      <CMAKE_INSTALL_PREFIX>はninja install を実行するために必要です。  
      ninja install でlib(so)ファイルとヘッダーファイルをコピーしてくれますので追加しましょう。  
      
      以下のような画像っぽい出力が出たらOKです。  
      コンソールをスクロールしていくと、「Looking for ~~」がありますので必要そうなヘッダーなどが見つかっているかを確認しましょう。  
      経験上、「EVP_aes_128_ctr」「CRYPTO_ctr128_encrypt」あたりのモジュールが見つからず、libsshのビルドに失敗し続けたことがあります。  
      この時の原因は「OPENSSL_CRYPTO_LIBRARY」を設定していなかったことでした。  
      それまでは、「android_openssl\static\lib\multiabi」までのディレクトリパスを「libssh_DIR」として設定していましたが意味が無かったようです。  
      (設定しても「[libssh_DIR]が見つからないから設定しろ」と永遠に怒られ続けていました)  
      
      出力内容がOKそうであれば以下のコマンドでビルドとインストールを実行します。  

``` cmake
ninja -j4 && ninja install
```

      以下画像のようになったら良さげです。  
      

    4. LINK
       
       ビルドが通り、ようやく「俺たちの戦いはこれからだ」となります。  
       これから「おいリンクエラーが出て実行できねーぞ、どうなってんだ！！」となるわけです。  
       特にqmakeではなくCMakeでCMakeする場合、意味がわからなくなります。  
       
       手始めに、Windowsでビルドを行う場合、libsshをインストールしたフォルダからQt Creatorのプロジェクトフォルダへincludeとlibをコピーします。  
       例えば以下のような構成になります。  

``` 
sampleSSH/
    ├ libssh_0.9.6/
    │    └ armeabi-v7a/
    │         ├ include/
    │         │  └ libssh/
    │         │      ├ libssh.h
    │         │      ├ ...
    │         │      ├ ...
    │         │      └ ...
    │         └ lib/
    │             └ libssh.so
    ├ CMakeLists.txt
    ├ CMakeLists.txt.user
    ├ main.cpp
    ├ main.qml
    └ qml.qrc
```
        これは、「ANDROID_EXTRA_LIBS」を設定する際に起きた事象なのですが、Windowsのパス"C:\hoge\hoge"を「ANDROID_EXTRA_LIBS」に設定して実行すると  
        Qt Creatorでのデプロイ時に「\\」が消えてしまいます。  
        つまり、「"C:\hoge\hoge"」は「"C:hogehoge"」として扱われてしまい、soファイルのコピーに失敗し、デプロイエラーとなります。  
        もっと新しいQt Creatorでは起きないかもしれませんが、Qt 5.15.2ではこの事象が起きてしまいます。  
        この事象を回避するには「/」を使えば良さそうであることが分かったので、「CMAKE_CURRENT_SOURCE_DIR」から参照が出来るようにコピーしておきます。  
        では、Qt CreatorでCMakeListsを使ってビルド・実行する場合、以下のような記述をCMakeLists.txtに行います。  
       
``` cmake
# for android
set(LIBSSH_ANDROID_ARMEABI_V7A ${CMAKE_CURRENT_SOURCE_DIR}/libssh_0.9.6/armeabi-v7a)
set(LIBSSH_ANDROID_H ${LIBSSH_ANDROID_ARMEABI_V7A}/include)
set(LIBSSH_ANDROID_LIB ${LIBSSH_ANDROID_ARMEABI_V7A}/lib)
set(ANDROID_EXTRA_LIBS ${LIBSSH_ANDROID_LIB}/libssh.so CACHE INTERNAL "Extra libraries to include in APK")
message("ANDROID_EXTRA_LIBS : " ${ANDROID_EXTRA_LIBS})
```
       messageでパスが大丈夫そうであることも確認します。  
       続いて、インクルードディレクトリの追加とshared objectも追加します。  
       
``` cmake
include_directories(${LIBSSH_ANDROID_H})
add_library(libssh SHARED IMPORTED)
set_target_properties(libssh PROPERTIES IMPORTED_LOCATION ${LIBSSH_ANDROID_LIB}/libssh.so)
```

       おそらく、Androidの場合、  

``` cmake
if(ANDROID)
    add_library(${PROJECT_NAME} SHARED ${PROJECT_SOURCES})
else()
    add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
endif()
```

        みたいな記述がされていると思います。  
        Androidでデプロイする場合、add_libraryで作成するようですので、そこに加えてあげると良さげです。

``` cmake
if(ANDROID)
    add_library(${PROJECT_NAME} SHARED ${PROJECT_SOURCES})
    include_directories(${LIBSSH_ANDROID_H})
    add_library(libssh SHARED IMPORTED)
    set_target_properties(libssh PROPERTIES IMPORTED_LOCATION ${LIBSSH_ANDROID_LIB}/libssh.so)
else()
    add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
endif()
```

         って感じ？  
         最後に、target_link_librariesを使ってlibsshもリンクします。  

``` cmake
target_link_libraries(${PROJECT_NAME} PRIVATE Qt${QT_VERSION_MAJOR}::Core Qt${QT_VERSION_MAJOR}::Quick libssh)
```
         
         みたいな？  


