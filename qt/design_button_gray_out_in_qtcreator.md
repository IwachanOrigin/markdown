
# Qt CreatorでDesign Buttonがグレーアウトしていて押せない

## 経緯

    Qt Quick のプロジェクトでQMLファイルを選択しているのにDesignボタンが押せない。  
    これではQMLのデザインを確認できない。  
    デザイナーで修正はしない方が良いとどこかのページで見かけた気がするのであくまでもプレビューのみの予定。  
    
## 対応方法

    Qt Creatorの場合、Qt Quickのプラグインが読み込まれていない可能性が高い。  
    以下のように辿って確認を行う。  
    
    メニューからHelp -> About plugins と選ぶ。  
![menu](https://user-images.githubusercontent.com/12496951/182552644-10a253df-cf0b-4af0-be66-ebe516fb7ad6.png)

    QML Designerが読み込まれているか？を確認する。読み込まれていない場合はチェックを付けて再起動する。  
![select_plugin](https://user-images.githubusercontent.com/12496951/182553443-9551fffa-01b6-41ff-959c-89da9e0a5bd5.PNG)

