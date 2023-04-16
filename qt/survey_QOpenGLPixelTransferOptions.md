
# survey QOpenGLPixelTranscferOptions

## Introduction

    QOpenGLPixelTranscferOptionsクラスを使用してQOpenGLTextureクラスのレンダリングを行う際の  
    注意点や調査した内容を記載します。  

## ImageHeightは3D Texture用

    あたかも画像の高さを設定しているような雰囲気を見せていますが、利用するのは「3DTexture」の場合のみです。  
    これは公式ドキュメントにも記載があります。「2DTexture」を扱う予定の場合は設定しても意味がありません。  

## YCbCr422のレンダリングについて

    OpenGLを用いた描画時もそうですが、YCbCr422の場合は1フレームあたり width * height * 2 がサイズになります。  
    RGBAは width * height * 4 です。  
    QOpenGLTextureクラス側でこの計算を行う方法は別に明示されていません。  
    この場合は「QOpenGLPixelTranscferOptions」クラスの「setRowLength」メソッドで調整を行います。  
    この関数は1行あたりのピクセル数を設定するためのものです。  
    
    実装方法によって、このあたりの計算は異なってくると思います。  
    ここでは、YCbCr422のデータをGPUへ転送し、ピクセルシェーダー上で色変換を行う場合の手法を示します。  
    
    データ自体はQOpenGLTextureクラスのsetData関数を利用して設定します。  
    このとき、フォーマットは「RGBA」を同時にセットしています。  
    RGBA は 「width * height * 4」の「4」を表しているような動きを取ることがデバッグで分かってきました。  
    そのため、setDataで送信されるデータ量は「width * height * 4」となってしまうようです。  
    この状態でYCbCr422の描画を行うと、メモリの範囲をオーバーするためクラッシュするか、  
    緑色の線が出たりする事象が発生します。  
    この事象を避けるため、1行あたりのピクセル数を「width / 2」とすることで正しくYCbCr422のデータを  
    復元できるように調整します。  
    
    このとき、setDataで設定するフォーマットを「RG」などの2色に設定すると「width / 2」する必要はありません。  
    ただし、「BA」がないため色はおかしくなります。  
    
