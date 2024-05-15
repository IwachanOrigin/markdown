
# runemacs daemon

## 紹介

    runemacs.exe に引数としてdaemonを与えた場合の挙動についてです。  

## environment

- Windows 10  
- Emacs 28.1  

## How to use

    batファイル、ショートカットなどで以下のような形にする。  
    runemacs.exe -daemon

## どうなる？

    taskbarにemacsのアイコンすら表示されず、タスクマネージャのバックグラウンドアプリに「emacs」が存在する。  
    serverモードを起動せずとも、デーモンとして起動するので「emacsclientw.exe」でファイルを開くようにしておくことで  
    起動しているデーモンに接続してファイルを開こうとしてくれる。  
    しかし、バッファをkillしたらemacs自体もバックグラウンドに隠れてしまう。  
    0.1秒くらい、起動速度も早くなる。  

## 難点

    バッファをkillしたらemacs自体もバックグラウンドに隠れてしまう点。  
    使わないバッファは癖でkillしてしまうのでemacs自体は隠さなくても良い。  
    

