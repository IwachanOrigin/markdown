
# ffmpeg build for android

## Introduction

    ffmpegをAndroidで利用するためのビルド方法を記載します。  

## Environment

    OS: Windows 10  
    WSL2  
    Ubuntu 22.04  

## 参考にするスクリプトなど

    ffmpeg-android-maker を利用します。  
[ffmpeg-android-maker](https://github.com/Javernaut/ffmpeg-android-maker)  

## 環境構築

    Windows上でAndroid用ffmpegをビルドするにはdockerを使うか、WSL2上のUbuntuなどを利用します。  
    ここでは、WSL2上のUbuntu 22.04を利用します。  

### Ubuntu 22.04 のインストール

    WSL2にはすでにインストール済みとします。  

### ビルド環境を整える

#### 注意

    この方法は、rootにAndroid-SDK等インストールしています。  
    これだと、スーパーユーザでビルドシェルを実行しなくてはなりません。  
    「ビルドできないぞ、どうしてだ？」に引っかかりやすいので、インストール先を変更したりするのが良いと思います。  

#### 手順

    ffmpeg-android-makerのreadme.mdに記載がありますが、dockerのスクリプトが参考になります。  

[dockerfile](https://github.com/Javernaut/ffmpeg-android-maker/blob/master/tools/docker/Dockerfile)  

    ファイルに記載されている内容をすべてシェル上で実行します。  
    ARGは外してOKです。  
    ENVは"export"に変更し、環境変数として設定しておきましょう。  
    ビルド時に利用します。  
    apt-getにはsudoが必要です。  
    もしくはシェル上でスーパーユーザーになってからコマンドを実行しましょう。  
    curl, mkdir あたりの処理でもrootに配置しているため、sudoが必要です。  
    "installAndroidComponent()"は、一時的な関数です。  
    後段の"installAndroidComponent "ndk;${VERSION_NDK}""などで利用しています。  
    この関数内で"${ANDROID_SDK_HOME}/cmdline-tools/bin/sdkmanager"を呼び出していますが  
    ここでもsudoが必要です。  

``` shell
installAndroidComponent() { yes | sudo ${ANDROID_SDK_HOME}/cmdline-tools/bin/sdkmanager --sdk_root=${ANDROID_SDK_HOME} "$1" > /dev/null; }
```

    上記関数まで処理が完了したら、環境構築は完了です。  

## ビルドする

    1. ffmpeg-android-makerをクローンします。  
       ディレクトリはubuntu側にする方が良いと思います。  

    2. ffmpeg-android-makerは以下の2つが設定されている前提でシェルが作成されています。  
       ANDROID_SDK_HOME  
       ANDROID_NDK_HOME  
       
       そのため、シェルの実行前に環境変数を一時的に設定します。  
       環境構築部分で"export"を使って設定している場合は気にしなくてOKです。  
       もし、変数として設定している場合は改めてexportしてください。  

    3. "ffmpeg-android-maker.sh" を実行します。  
       このとき、Android SDKをrootにインストールしている場合、スーパーユーザで実行が必要です。  
       rootにインストールしている場合、以下画像のようなエラーが表示されます。  
![shell_run_failed](https://user-images.githubusercontent.com/12496951/195578012-070d4f48-1a6c-4965-b6a1-7ffbd14ebbd1.PNG)  

    4. ビルドが成功すると、outputフォルダへincludeフォルダとlibフォルダが生成されます。  
![shell_fun_success_1](https://user-images.githubusercontent.com/12496951/195578959-1a05af53-bb6b-4ca6-97d3-54953979d7d8.PNG)  
![shell_fun_success_2](https://user-images.githubusercontent.com/12496951/195578960-f626f3a7-20a3-4189-b358-d74d8e5e5f4e.PNG)  

