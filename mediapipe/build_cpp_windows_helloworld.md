
# mediapipe build with cpp : Hello, World!

## Overview

mediapipe を Windows10 でビルドしようとすると非常に苦労したのでその備忘録です。  
Hello World が出来るところまでの手順を記載します。  
WSL2は利用しません。  

## Enviroment

OS: Windows10 Pro  
MSYS2 : 20230718 version  
Python : Anaconda3 version (version 3.11.0)  
Visual Studio : 2022 Professional(17.7.4)  
Visual Studio Buildtools : 17.74  
WINSDK : 10.0.22621.0  
OpenCV : 3.4.10  
Bazel : 6.3.2  

## NOTE 1

基本的なインストール手順はGoogleの公式ドキュメント通りです。  
[https://developers.google.com/mediapipe/framework/getting_started/install](https://developers.google.com/mediapipe/framework/getting_started/install)  

## NOTE 2

公式のトラブルシューティングも参照ください。  
[https://developers.google.com/mediapipe/framework/getting_started/troubleshooting.md](https://developers.google.com/mediapipe/framework/getting_started/troubleshooting.md)  

## NOTE 3

このビルド方法は 2023/09/26 時点で成功した方法です。  

## Step

### Step1 : MSYS2 のインストールと環境変数へ追加する

MSYS2をインストールします。  
以下のMSYS2公式サイトからインストーラをダウンロードし、インストールしてください。インストール先は任意です。  
[https://www.msys2.org/](https://www.msys2.org/)  

インストール完了後、以下の部分を環境変数へ追加します。  

    C:/<path to install>/usr/bin  

デフォルトの場合は以下になると思います。  

    C:/msys64/usr/bin  

このフォルダ内の「bash.exe」をコマンドプロンプトから実行したいがために追加しています。  

### Step2 : pacmanを使って必要な資源のインストール

pacman を用いて、ビルドに必要な資源をインストールします。  
環境変数へパスを追加した後であれば、MSYS2でもコマンドプロンプトでもどちらでもOKです。  
以下のコマンドを実行します。  

    pacman -S git patch unzip  

### Step3 : Pythonのインストール

BazelはPythonを利用するスクリプトらしいのでインストールします。  
AnacondaでもPython単体でもどちらでも良いようです。  
ここではAnacondaを使いました。  
Anacondaは以下のURLからインストーラをダウンロードしてインストールします。  
[https://www.anaconda.com/download](https://www.anaconda.com/download)  

バージョンはその時の最新でした。Anaconda3-2023.07-2-Windows-x86_64.exe です。  
インストール先も任意です。  
私の場合、C:\software ってフォルダにまとめるのが得意なので、C:\software\anaconda3 にインストールしました。  

### Step4 : Visual Studio Build Tools のインストール

Visual Studio Build Tools をダウンロードしてインストールします。  
[https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/](https://visualstudio.microsoft.com/ja/visual-cpp-build-tools/)  

Visual Studio 本体はすでにインストール済みであるとして進めます。  
そのため、WinSDKのインストールについては記載しません。  

### Step5 : OpenCVのインストール

OpenCV をインストールします。  
MediaPipe でサポートされているバージョンは3.4.10 です。  
以下のリンクより探してダウンロードします。  
[https://opencv.org/releases/](https://opencv.org/releases/)  

もしかしたら、4系でも動くかもしれませんが試していません。  
3.4.10以外のバージョンの場合、後述するWORKSPACEファイルの修正とは別でもう1ファイル修正が必要になります。  

### Step6 : Bazel のダウンロードと配置

Bazel をインストールします。  
bazelisk が推奨されていますが、このソフトウェアの使い方は意味不明ですのでBazelを使います。  
Bazel も意味不明ですが、bazeliskよりはマシです。　　

Bazelを以下よりダウンロードします。  
[https://github.com/bazelbuild/bazel](https://github.com/bazelbuild/bazel)  

githubのページですので、latest をダウンロードしましょう。  
私は6.3.2 をダウンロードしましたので、ファイル名は「bazel-6.3.2-windows-x86_64.exe」になります。  

ダウンロードしたら、任意のフォルダへ配置します。  
配置後、ファイル名は「bazel.exe」にリネームします。  
リネーム後、環境変数へフォルダを追加します。  

私の場合、C:\software\bazel を環境変数へ追加しています。  

環境変数へ追加したら動作確認します。  
コマンドプロンプトを起動し、以下のコマンドを実行します。  

    basel version

コマンドを認識し、バージョン情報が出たらOKです。  

### Step7 : Bazel のための環境変数設定

BazelでVisual Studioのコンパイラを使うには環境変数を設定する必要があります。  
5つ程、追加します。  

    set BAZEL_VS=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools
    set BAZEL_VC=C:\Program Files\Microsoft Visual Studio\2022\Professional\VC
    set BAZEL_VC_FULL_VERSION=14.37.32822
    set BAZEL_WINSDK_FULL_VERSION=10.0.22621.0
    set BAZEL_SH=C:\software\msys64\usr\bin\bash.exe

これは、Visual Studio 2022 Professional をデフォルトでインストールした場合のパスとなります。  
環境変数について簡単に説明します。  

BAZEL_VS  
Visual Studio Build Toolsまでのパスです。  

BAZEL_VC  
Visual Studio VC までのパスです。  
もし、Visual Studio Build Toolsのみのインストールでチャレンジする場合、Build ToolsのVCフォルダを設定します。  
その場合、以下のようになります。  

    set BAZEL_VC=C:\Program Files (x86)\Microsoft Visual Studio\2022\BuildTools\VC

BAZEL_VC_FULL_VERSION  
Visual Studio VC のバージョンを記載します。  
この値はオプションのようです。未指定の場合、最新のものを勝手に使用してくれます。  
2022の場合、以下のパスにバージョンが記載されたフォルダがありますので、そちらを利用しました。  

    C:\Program Files\Microsoft Visual Studio\2022\Professional\VC\Tools\MSVC

ここでは「14.37.32822」とします。  
また、ここのバージョンが間違っていると、Bazelはcl.exeなどを見つけることが出来ず、永遠にエラーを吐き続けます。  
全く気が利かない糞ツールです。  

BAZEL_WINSDK_FULL_VERSION  
Windows SDK のバージョンを記載します。  
この値はオプションのようです。未指定の場合、最新のものを勝手に使用してくれます。  
Visual Studio をインストールした際、一緒にインストールされるのでそちらを利用します。  
以下パスにインストールされているバージョンを使用しました。  

    C:\Program Files (x86)\Windows Kits\10\Lib

ここでは、「10.0.22621.0」とします。  
まだ間違えたことはありませんが、ここのバージョンが間違っていると、Bazelは狂うはずです。  
なぜなら、そういうツールだからです。くそ。  

BAZEL_SH  
WSLをインストールして利用されている場合、この値を設定しないとPythonを見つけることが出来ません。  
BAZELで利用するbash.exeのパスを指定します。  
ここでは、MSYS2のbashを利用しますので、以下のパスを使用します。  

    C:\software\msys64\usr\bin\bash.exe

この設定を行わない場合、WSL側のbash.exeが起動してしまいます。  
この時、気を利かせてダブルクオーテーションでパスを括ってしまうとエラーが発生し、まともに動かなくなります。  
どうやら、内部でも気を利かせてダブルクオーテーションで括ってくれているため、二重に括ることになります。  
結果、Pythonのプロセス起動関数が実行に失敗します。  

### Step8 : MediaPipe をダウンロード

リポジトリをクローン or ソースファイルをダウンロードします。  
[https://github.com/google/mediapipe](https://github.com/google/mediapipe)  


### Step9 : WORKSPACE の修正

クローン or ダウンロードしてきたフォルダパスの直下に存在するWORKSPACE をエディタで開きます。  
350行目あたりにWindows OpenCVの設定があるので、OpenCVをインストールしたフォルダパスへ書き換えます。  

Before:  
``` yarn
    new_local_repository (
        name = "windows_opencv",
        build_file = "@//third_party:opencv_windows.BUILD",
        path = "C:\\opencv\\build",
    )
```


After:  
``` yarn
    new_local_repository (
        name = "windows_opencv",
        build_file = "@//third_party:opencv_windows.BUILD",
        path = "C:\\software\\opencv_3.4.10\\build",
    )
```

### Step10 : opencv_windows.BUILD の修正

OpenCV 3.4.10 を利用されている場合は次のステップへ進んでください。  
3.4.10 以外を使用されている場合は以下フォルダの内容を修正する必要があります。  

    <path to mediapipe>/mediapipe/third_party/opencv_windows.BUILD  

バージョンの記載があるので、その部分を変更します。  

``` yarn

exports_files(["LICENSE"])

OPENCV_VERSION = "3410"  # 3.4.10

```

### Step11 : mediapipe のビルド

コマンドプロンプトを起動し、MediaPipe のフォルダへ移動し、ビルドを行います。  

    cd <path to mediapipe>/mediapipe
    bazel build -c opt --define MEDIAPIPE_DISABLE_GPU=1 --action_env PYTHON_BIN_PATH="C://software//anaconda3//python.exe" mediapipe/examples/desktop/hello_world

Windows でビルドする場合は「--action_env」を付けます。  
PYTHON_BIN_PATH にはPythonのバイナリまでのパスを設定してください。  
前述の通り、実行するShellの設定がMSYS2のbash.exeになっていないと、Python.exeが見つからないエラーを吐きます。  
上手くいけば、ビルドは正常終了します。  

### Step12 : yarn_install でエラー

WORKSPACEのyarn_install で謎のエラーが出ることがあります。  
フォルダパスを除いて一部抜粋します。  

    cynsstkq/external/build_bazel_rules_nodejs/internal/npm_install/npm_install.bzl:1002:31: in <toplevel> ERROR: An error occurred during the fetch of repository 'npm':
        Traceback (most recent call last):
            File "cynsstkq/external/build_bazel_rules_nodejs/internal/npm_install/npm_install.bzl", line 993, column 13, in _yarn_install_impl
                fail("yarn_install failed: %s (%s)" % (result.stdout, result.stderr)) Error in fail: yarn_install failed:
                ('"YARN_IGNORE_PATH="' は、内部コマンドまたは外部コマンド、 操作可能なプログラムまたはバッチ ファイルとして認識されていません。
                'ﾃ｢ﾂ€ﾂ廬NIT_CWD' は、内部コマンドまたは外部コマンド、 操作可能なプログラムまたはバッチ ファイルとして認識されていません。 )

これは、yarn_install のスクリプト側がバグっている影響で文字化けしてしまい、yarnのインストールに失敗してしまうようです。  
このクソを解決するには、上述ファイルのbzlファイルを修正する必要があります。  
ローカル環境にbazelのファイルがダウンロードされていると思いますので、エラーログから環境に合わせてファイルを探してください。  
私の場合は以下パスにありました。  

    "C:/Users/<USER NAME>/_bazel_<USER NAME>/cynsstkq/external/build_bazel_rules_nodejs/internal/npm_install/npm_install.bzl"

950行目あたりを確認します。  

``` bazel
set "YARN_IGNORE_PATH="
set “INIT_CWD=”
set “npm_config_registry=”
```

このような記述になっていますが、「INIT_CWD」、「npm_config_registry」を囲んでいるダブルクオーテーションが日本語環境だとおかしくなってしまうようです。  
正しく、「"」で囲んでください。  

``` bazel
set "YARN_IGNORE_PATH="
set "INIT_CWD="
set "npm_config_registry="
```

ファイルは読み取り専用になっていますので、読み取り専用チェックを外すか、別名保存後に上書きしてください。  

修正後、再度Step11のコマンドを実行します。  

### Step13 : GetMessageA はありませんエラー

Windows でビルドする場合、GetMessageはマクロで定義されています。  
mediapipe/framework/tool/proto_util_lite.cc 内の「FieldAccess」クラスには「GetMessage」という同一名称の関数が存在します。  
マクロが邪魔をして「Getmessage」を「GetMessageA」 or 「GetMessageW」へと解釈してしまい、関数が見つからないエラーが出ます。  
proto_util_lite.cc - 140行目あたりに「FieldAccess::GetMessage」の定義がありますので、その直上あたりに「#undef GetMessage」を追加します。  
これで、GetMessageマクロが無効になります。  

``` c++
absl::Status FieldAccess::SetMessage(const std::string& message) {
  ArrayInputStream ais(message.data(), message.size());
  CodedInputStream in(&ais);
  StringOutputStream sos(&message_);
  CodedOutputStream out(&sos);
  return GetFieldValues(field_id_, &in, &out, &field_values_);
}

#undef GetMessage

void FieldAccess::GetMessage(std::string* result) {
  *result = message_;
  StringOutputStream sos(result);
  CodedOutputStream out(&sos);
  WireFormatLite::WireType wire_type =
      WireFormatLite::WireTypeForFieldType(field_type_);
  SetFieldValues(field_id_, wire_type, field_values_, &out);
}

```

修正後、再度Step11のコマンドを実行します。  

### Step14 : Hello World

Hello World のバイナリファイルが出来ているはずですので、移動して実行してみます。  

    cd bazel-bin\mediapipe\examples\desktop\hello_world
    set GLOG_logtostderr=1
    hello_world.exe

set GLOG_logtostderr=1 を行わないとコンソール出力されないようです。  
正常に実行された場合、10回Hello World! が表示されます。  



