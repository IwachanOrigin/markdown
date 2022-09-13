
# Setting for MaintenanceTool.exe

## 紹介

    あーメンテナンスツール経由でモジュールをインストールしなくちゃーってなったとき、メンテナンスツール起動童貞だった場合に  
    Qt関連のURLなどを設定する必要があります。  
    毎回、それを忘れてググってしまうので、書いておきます。  
    
## 5.15.2以降(?)

    5.15.2以降?のメンテナンスツールを起動しても、「この操作を正常に実行するには、有効なリポジトリが1つ以上必要です。」は出なくなったっぽいです。  

## それはなんぞ？

    こういうやつです。リポジトリを登録しないとダメらしいです。  
![maintenancetool1](https://user-images.githubusercontent.com/12496951/189837477-b52e7cf1-e09e-4d58-87f3-bd9d94f3c878.png)  

## 何をすれば良いの？

    1. UI左下から「設定」を選択します。  
    2. リポジトリタブを選択します。  
    3. 「ユーザー定義のリポジトリ」を選択し、「追加」ボタンを押下します。  
![maintenancetool2](https://user-images.githubusercontent.com/12496951/189838113-87f21102-1d4c-4df2-9163-429bd3b5b269.png)  

    4. 以下のURLを入力します。  
    http://download.qt-project.org/online/qtsdkrepository/windows_x86/desktop/tools_maintenance

    5. 「テスト」を押下し、以下のダイアログが出たらOKです。  
![maintenancetool3](https://user-images.githubusercontent.com/12496951/189838616-40c5e475-ba2a-444b-8da1-2b8b7c87fdc4.png)  


