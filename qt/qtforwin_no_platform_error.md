
# Qt for windows, no Qt platform plugin could be initialized error

## Introduction

    Windows上でQt CreatorやVisual Studio上からQtを使って作ったソフトウェアをデバッグ実行することは出来るのに  
    実行ファイル(.exe)を単体で実行すると「no Qt platform plugin could be initialized」というエラーが  
    発生する場合の解決方法です。  
    グーグリングしても「OneDriveが起動しない」などの情報ばかりが検索上位に来て苦しんだので書いておきます。  

![qt_error](https://user-images.githubusercontent.com/12496951/185541428-b9d98dd3-0476-434e-89e0-513177433210.PNG)

## Environment

    Qt : 5.15.2
    OS : Windows 10 21H2 (19044.1889)


## 考えられる原因

    IDEからのデバッグ実行は出来ているのにExe単体で実行する場合のみ失敗するので、必要なDLLファイルがExeの求める階層に  
    存在しないためだと考えられます。  
    IDE経由での実行すら出来ない場合は、違う原因が考えられますのでご注意ください。  
    
## 解決方法

    2つくらいあります。  
    「QTDIR」という環境変数が登録されている前提でお話を進めます。  
    Release構成の場合、DLLファイルの名称が異なります。  
    登録されていない場合、フルパスに読み替えてください。  
    私の環境では以下としています。  
    QTDIR=C:\\software\\Qt\\5.15.2\\5.15.2\\msvc2019_64  

### 方法1

    手っ取り早いのは「windeployqt.exe」を使ってコピーしてもらう方法です。  
    widgetsを利用している場合は以下のコマンドを使用します。  
'''Batchfile
%QTDIR%/bin/windeployqt.exe [exe file]
'''

    QMLを利用している場合は以下のコマンドを使用します。  
'''Batchfile
%QTDIR%/bin/windeployqt.exe --qmldir %QTDIR%/qml [exe file]
'''

    コマンドが正常に終了すれば、指定したExeファイルと同じ階層に必要そうなディレクトリやdllなどを全てコピーしてくれます。  
    ただし、あまりにも量が多かったり、実際にはいらないだろうファイルもコピーされます。  
    QMLの場合、もっと増えてしまいます。  

### 方法2

    自分で必要そうなディレクトリやDLLを探してコピーします。  
    方法1でコピーした後にいらなさそうなファイルを一つずつ消しても良いかもしれません。  
    起動しなくなる可能性はもちろんあります。  
    また、エラーを吐いてくれていたDLLをすべて配置してもうんともすんとも言わなくなることもあります。  
    Core, GUI, Widgetsだけを利用している最小構成の場合、以下のような構成で起動するようです。  

    AppName/
        ├ platform/
        │        └ qwindowsd.dll
        │
        ├ AppName.exe
        ├ Qt5Cored.dll
        ├ Qt5Guid.dll
        └ Qt5Widgetsd.dll

![qt_error_solved_tech_1](https://user-images.githubusercontent.com/12496951/185541468-a354fed4-96d5-4320-89c8-5f2a7b75a45c.PNG)  
![qt_error_solved_tech_2](https://user-images.githubusercontent.com/12496951/185541554-07c4362c-f024-4f14-8ff9-ee637171d2e7.PNG)  

    QMLは量が膨大なので、考えることを止めました。  

## 終わり

    ちょっとサンプル用に作ったexeを異なるPCで動かそうとしただけなのにwindeplyqt.exeを使う必要があるのは  
    とてもめんどくさいなぁと思います。  
    何かほかに良い方法があると良いのですが...。  
    
