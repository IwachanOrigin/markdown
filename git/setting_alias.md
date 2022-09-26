
# setting for git alias

## Overview

    開発を行う際、aliasにしておくとちょっとだけgitが扱いやすくなるような気がするtipsを記載します。  

## Enviroment

    OS: Windows10 Pro
    Software: WSL2(Ubuntu 22.04)

## How to make

    .bashrcにぶち込みます。  

## git status

    リポジトリの状態を表示します。よく使うやつです。  
    キーボードを打つのが面倒くさいので単純な短縮コマンドにします。  

``` shell
alias gs='git status'
```

## git log

    時系列で何してたっけ？とか、設計したりボトムアップでパーツを作っていると今はどこまでできているんだろう？と  
    確認したくなります。そういうときは、git logをワンラインで表示すると便利です。  
    ここでは、日時、コミットハッシュ、コメントを表示します。  
    コメントには基本的に「何をやったのか？ どんな変更を行ったか？」を書くことが前提です。  
    
``` shell
alias gl="git log --oneline --pretty=format:'%C(auto)%h %C(cyan)[%cd] %C(auto)%d %s <%an>' --date=format:'%Y/%m/%d %H:%M:%S'"
```

    このコマンドでコミットハッシュがわかるので、ビルドや実行結果がおかしくなってしまった場合は過去に戻すことも容易です。  

