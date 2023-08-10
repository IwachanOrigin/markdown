
# QtCreator change build dir

## 紹介

    QtCreatorでビルドするとデフォルトでは謎のディレクトリを生成、バイナリファイルを出力します。  
    これが嫌だったので設定を変更できる方法を調査しました。  

## 同じ悩みを抱えている方の解決方法

[Qtのビルドディレクトリが鬱陶しい話](https://louis-needless.hatenablog.com/entry/gloomy-build-directory)  

## Qt Creator バージョン

    11.0.0  

## 変更方法

1. Qt Creatorを起動します。  
2. 編集 -> Preferences... -> ビルドと実行 -> Default Build Properties へ移動します。  
3. 既定のビルドディレクトリの内容を変更します。  

    ./build/%{JS: Util.asciify("%{BuildConfig:Name}")}

上述の設定を反映した場合は、以下のツリー構造になります。  

<pre>  
ProjectDirectory  
    └---build  
        ├---Debug  
        │    └---xx.exe  
        │  
        └---Release  
             └---xx.exe  
</pre>  

## NOTE

古いQtCreatorの場合、設定などの配置場所が異なる場合がありますので注意してください。  

