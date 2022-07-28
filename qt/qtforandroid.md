
# Qt for Android

## 設定についての注意

[LINK](https://qthub.com/static/doc/qt5/qtdoc/android-getting-started.html)

    上記リンクに記載があるのだが、Qtのバージョンによって利用できるNDKが異なる。  
    以下、転記。  
    Qt 5.14.0 or later, Qt 5.13.2 or later, and Qt 5.12.6 or later
      - NDK r20b or r21
      - android-clang
      - Note: Qt 5.14.0 and 5.14.1 with NDK r21 have a bug fixed at QTBUG-81461.

    Qt 5.12.0 to 5.12.5 and Qt 5.13.0 to 5.13.1
      - NDK r19c
      - android-clang

    Qt 5.11 and below
      - NDK r10e
      - GCC toolchain
      
    QtのバージョンページごとにあるQt for Androidページにも一応記載はあるのだが、とても読みにくい。  
    
[Qt for Android(qt5-5.15)](https://doc-snapshots.qt.io/qt5-5.15/android.html)

    Qt 5.15の場合、APIレベルは21以上となる。なので、NDKはndk21を利用する。らしい。  
    Qt 6からは記述の内容が詳しくなり、ndkはどれを使えるのかがもう少し分かるようになった。  

    また、別途参考にしたブログ(?)記事は以下。  
[Blog Link](https://developernote.com/2021/12/configuring-android-ndk-version-in-qt-creator/)

    githubのandroidページにはndkのダウンロードリンクが存在する。ただし、これは別に使わなくてもSDKManager toolや  
    Android Studio経由でもダウンロードは可能。  
[Unsupported-Downloads](https://github.com/android/ndk/wiki/Unsupported-Downloads)


    Windows環境下でQtCreatorがndkを自動的に見つけたり、どのndkをデフォルトで使用するのか？を定義している場所は以下のパス。  
    C:\Users\<USER NAME>\AppData\Roaming\QtProject\qtcreator\android
    sdk_definitions.json

    読むとわかるのだが、「"specific_qt_versions":」という部分で設定しているっぽい。  
    変更が必要な場合はバックアップを作成して退避しておくこと。  
    
