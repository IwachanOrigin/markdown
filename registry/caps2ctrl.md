
# Caps2Ctrl

## 内容

レジストリを弄って、CapsキーをCtrlキーとして認識するようにします。  
Surface Pro(11世代) Snapdragon X Elite でも動作することを確認済み。  

## 設定

### レジストリエディターを開く
「regedit」で開きます。  

### キーの移動

以下のキーへ移動します。  
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Keyboard Layout  

### バイナリ値の追加
新しいバイナリ値を追加します。  
キー値 : Scancode Map  
値 : 00 00 00 00 00 00 00 00 02 00 00 00 1d 00 3a 00 00 00 00 00  

値はコピー＆ペーストでは貼り付けることが出来ません。  
キーボードから16進数で入力します。  

### PCの再起動
設定が完了したらPCを再起動します。  


### 動作確認
再起動後、CapsキーがCtrlキーとして動作することを確認します。  


## 参考
[https://github.com/penguin2716/Caps2Ctrl](https://github.com/penguin2716/Caps2Ctrl)  
[https://github.com/randyrants/sharpkeys](https://github.com/randyrants/sharpkeys)  


