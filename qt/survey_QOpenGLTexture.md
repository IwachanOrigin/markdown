
# survey QOpenGLTexture

## Introduction

    QOpenGLTextureクラスを使用してレンダリングを行う場合の注意点や調べてわかったことなどを記載します。  

## QImageでコンストラクタを初期化する際の注意

    画像を簡単に表示できるQImageで生成したデータをコンストラクタにセットする方法はとても便利です。  
    フォーマットなども気にすることなく設定してくれるのでちょっとした確認にも使えて便利です。  
    しかし、テストのためにQImageから生成したQOpenGLTextureクラスのサイズを変更することはできません。  
    これは、QOpenGLTextureクラスの実装を読むと判明しますが、  

``` c++
setFormat(RGBA8_UNORM);
setSize(image.width(), image.height());
setMiplevels(...);
allocateStrage();
.....
```

    上述の通り、内部的に「allocatestrage関数」を呼び出しています。  
    この点は注意しましょう。  

## 破棄して再作成

    allocateStrage関数を呼び出してしまったら、解除することはできないようです。  
    この場合はオブジェクトを破棄し、改めてQOpenGLTextureオブジェクトを作成するしかないようです。  

