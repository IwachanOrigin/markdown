
# QRC in static libraries

## QRCファイルを静的ライブラリに組み込んだ場合の注意  

静的ライブラリ化したプログラムの中にQRCファイルを組み込んだ場合、「Q_INIT_RESOURCE」マクロにて明示的に  
QRCファイルを読み込んであげる必要があります。  

例えば、フォルダ構成が以下のようになっていた場合を考えます。  

+---pageMain
|   |   CMakeLists.txt
|   |   mainwindow.cpp
|   |   mainwindow.hpp
|   |   mainwindow.qrc
|   |   mainwindow.ui

「CMakeLists.txt」では、mainwindowクラスを静的ライブラリ化し、実行ファイルへリンクします。  
この状態でもビルドは通り、実行も可能です。ただし、UIなどに埋め込んでいた画像ファイルなどは表示されません。  
QDebugなどを用いて以下のようなファイルリソースパスの確認を行っても結果は「false」になります。  

``` c++
qDebug() << "File check : " << QFile::exists(":/Hoge/hoge.png");
```

これを避けるには、プログラム内で明示的にQRCファイルを読み込む必要があります。  

``` c++
MainWindow::MainWindow(QWidget *parent)
  : QMainWindow(parent)
  , ui(new Ui::MainWindow)
{
  ui->setupUi(this);
  Q_INIT_RESOURCE(mainwindow);

  this->setupPage();
  this->eventConnect();
}
```

上述のコードでは、「mainwindow.qrc」を読み込むコードになります。  
フォルダ階層が異なる場合に、どのような書き方が必要なのかは未検証です。  

[https://doc.qt.io/qt-6.5/resources.html](https://doc.qt.io/qt-6.5/resources.html)

