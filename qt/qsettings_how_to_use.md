
# QSettings How to use

## 経緯

    クロスプラットフォームで設定ファイルを気にせずに使う方法を模索する中で見つけました。  
    その設定方法など、ドキュメントを読むだけではわからない部分がありました。  
    そのあたりをまとめます。  
    
## まず最初に読むもの

    公式のドキュメントを読む。  
[Qt5-QSettings](https://doc.qt.io/qt-5/qsettings.html)

    上記のURLはQt5のもの。Qt6以上の場合は注意。  

## 使うときの注意点

    主だった使い方の説明は公式ドキュメントに書いてある。  
    ここには注意点を記載する。  
    
    - 組織名などの設定を忘れない。  
```cpp
QCoreApplication::setOrganizationName("MySoft");
QCoreApplication::setOrganizationDomain("mysoft.com");
QCoreApplication::setApplicationName("Star Runner");
```

    - ファイルパスなど、出力するパスやファイル名を明確に設定していない場合、設定の格納先は  
      OS毎の標準設定となる。  
      Windowsの場合はレジストリとなる。  
      出力先は以下のような形。  
      コンピューター\HKEY_CURRENT_USER\SOFTWARE\<設定した組織名>\<設定したアプリケーション名>  

