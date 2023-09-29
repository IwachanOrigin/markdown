
# mediapipe build with cpp : HandTracking

## Overview

mediapipe を Windows10 でビルドしようとすると非常に苦労したのでその備忘録です。  
HandTracking が出来るところまでの手順を記載します。  
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
Mediapipe : 0.10.5  

## NOTE 1

基本的なインストール手順はGoogleの公式ドキュメント通りです。  
[https://developers.google.com/mediapipe/framework/getting_started/install](https://developers.google.com/mediapipe/framework/getting_started/install)  

## NOTE 2

公式のトラブルシューティングも参照ください。  
[https://developers.google.com/mediapipe/framework/getting_started/troubleshooting.md](https://developers.google.com/mediapipe/framework/getting_started/troubleshooting.md)  

## NOTE 3

このビルド方法は 2023/09/29 時点で成功した方法です。  
Mediapipe のバージョンは「0.10.5」です。  

## Step

### Step1 : MSYS2 のインストールと環境変数へ追加する
### Step2 : pacmanを使って必要な資源のインストール
### Step3 : Pythonのインストール
### Step4 : Visual Studio Build Tools のインストール
### Step5 : OpenCVのインストール
### Step6 : Bazel のダウンロードと配置
### Step7 : Bazel のための環境変数設定
### Step8 : MediaPipe をダウンロード
### Step9 : WORKSPACE の修正
### Step10 : opencv_windows.BUILD の修正

Hello, World! 実行までの手順と同じです。  
そちらを参照ください。  

### Step11 : mediapipe のビルド

コマンドプロンプトを起動し、MediaPipe のフォルダへ移動し、ビルドを行います。  

    cd <path to mediapipe>/mediapipe
    bazel build -c opt --define MEDIAPIPE_DISABLE_GPU=1 --action_env PYTHON_BIN_PATH="C://software//anaconda3//python.exe" mediapipe/examples/desktop/hand_tracking:hand_tracking_cpu

Windows でビルドする場合は「--action_env」を付けます。  
PYTHON_BIN_PATH にはPythonのバイナリまでのパスを設定してください。  
前述の通り、実行するShellの設定がMSYS2のbash.exeになっていないと、Python.exeが見つからないエラーを吐きます。  
上手くいけば、ビルドは正常終了します。  

### Step12 : yarn_install でエラー

Hello, World! 実行までの手順と同じです。  
そちらを参照ください。  

### Step13 : GetMessageA はありませんエラー

Hello, World! 実行までの手順と同じです。  
そちらを参照ください。  

### step14 : tflite ファイルのダウンロード

バイナリファイルが生成されたので起動してみますが、以下2ファイルが見つからないため、起動に失敗します。  
- hand_landmark_full.tflite
- palm_detection_full.tflite

ダウンロード先はそれぞれ、以下となります。  
- hand_landmark_full.tflite  
[https://storage.googleapis.com/mediapipe-assets/hand_landmark_full.tflite](https://storage.googleapis.com/mediapipe-assets/hand_landmark_full.tflite)  

- palm_detection_full.tflite  
[https://storage.googleapis.com/mediapipe-assets/palm_detection_full.tflite](https://storage.googleapis.com/mediapipe-assets/palm_detection_full.tflite)  

これらのファイルのダウンロード先がどこなのか、公式ドキュメントなどを見ていてもよくわかりません。  
githubのページ上部にある「Search this repository」ボックスにてそれぞれのファイル名を入力すると、「docs/solutions/models.md」などを見つけることが出来ます。  
非常にクソです。  
格納先のディレクトリには「BUILD」がありますので、もしかしたらbazelのパラメータ指定でダウンロードできるのかもしれません。  
特に方法の記載を見つけることは出来ませんでした。  

ダウンロードしたファイルは、それぞれ以下のディレクトリへ格納します。  
- hand_landmark_full.tflite  
<path/to/mediapipe>/mediapipe/modules/hand_landmark  

- palm_detection_full.tflite  
<path/to/mediapipe>/mediapipe/modules/palm_detection  

### Step15 : Run hand_tracking_cpu

hand_tracking_cpu のバイナリファイルを実行します。  

    set GLOG_logtostderr=1
    bazel-bin\mediapipe\examples\desktop\hand_tracking\hand_tracking_cpu --calculator_graph_config_file=mediapipe/graphs/hand_tracking/hand_tracking_desktop_live.pbtxt

set GLOG_logtostderr=1 を行わないとコンソール出力されないようです。  
また、「calculator_graph_config_file」のパス指定を行うため、bazel-binのショートカットがあるディレクトリで起動を行います。  
起動までに多々、時間が必要です。  

``` text
I0000 00:00:1695969788.874349   26544 demo_run_graph_main.cc:56] Initialize the calculator graph.
I0000 00:00:1695969788.883797   26544 demo_run_graph_main.cc:60] Initialize the camera or load the video.
```

上記出力がコンソールに表示されてから30秒程度の時間が必要です。  
待っていると「MediaPipe」というOpenCVウィンドウが表示されます。  
そこからさらに30秒～60秒待つと接続されているカメラデバイスの0番目をキャプチャして解析を行います。  
起動までの動画を撮影しましたが、1分30秒程度掛かります。  


### Appendex : rectangle.h の名前衝突によるビルドエラー

2023/09/26時点での最新をクローンすると、Windowsの場合、rectangle.h が WinGDI.h の Rectangle関数と衝突してしまいます。  
(0.10.5 だと起きませんでした。何故...？)  

この場合、以下のファイルへnamespaceを付与する必要があります。  
<path/to/mediapipe>/mediapipe/framework/deps/rectangle.h  

``` c++
namespace mediapipe {

/* 全体を含める */

}
```

<path/to/mediapipe>/mediapipe/util/rectangle_util.h  

``` c++
// Computes the Intersection over Union (IoU) between two rectangles.
// 「Rectangle_f」の前に「mediapipe::」を付与する
float CalculateIou(const mediapipe::Rectangle_f& rect1, const mediapipe::Rectangle_f& rect2);

```

<path/to/mediapipe>/mediapipe/util/rectangle_util.cc  

``` c++
// 「Rectangle_f」の前に「mediapipe::」を付与する
float CalculateIou(const mediapipe::Rectangle_f& rect1, const mediapipe::Rectangle_f& rect2) {
```

#define NOGDI も試しましたが、私の環境では意味がありませんでした。  
この方法で2023/09/26時点で最新だったバージョンではビルドが通りました。  
その後でダウンロードしていたv0.10.5のビルドを試して、このエラーが出ませんでした。  


