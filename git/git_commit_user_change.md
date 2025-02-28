
# Git commit user change

## Overview

個人アカウントでコミットしたと思っていたら、別のアカウントでコミットしていた場合に、ユーザー情報をすべて修正する方法。  

## 参考ページ
[https://qiita.com/y10exxx/items/dcea0e39788d649ca8ba](https://qiita.com/y10exxx/items/dcea0e39788d649ca8ba)  

## どのアカウントでコミットされているのかを確認する

``` shell
git log --pretty=full
```

以下のような情報が出てきます。  

``` shell
commit xxxxx085dd658593ca4c42f81ea636134171cec7 (HEAD -> master, origin/master)
Author:     abc <abc@xxx.com>
AuthorDate: Fri Feb 1 00:22:06 2019 +0900
Commit:     abc <abc@xxx.com>
CommitDate: Fri Feb 1 00:22:06 2019 +0900
```

記事によると以下の通り。  
>このとき、
>Authorは、オリジナルのCode作成者
>Committerは、コミットをした人
>になる。
>gitは、rebaseやcommit --amendを用いてhistoryを変更できる場合がある。なので元の作成者を分けて管理しているらしい。

## 過去のコミットを改変する

``` shell
git filter-branch -f --env-filter \
  "GIT_AUTHOR_NAME='new'; \
   GIT_AUTHOR_EMAIL='new@example.com'; \
   GIT_COMMITTER_NAME='new'; \
   GIT_COMMITTER_EMAIL='new@example.com';" \
  HEAD
```

各項目の内容を正しいものに修正して実行します。  
実行時、以下の警告文が表示されます。  

``` shell
WARNING: git-filter-branch has a glut of gotchas generating mangled history rewrites. Hit Ctrl-C before proceeding to abort, then use an alternative filtering tool such as 'git filter-repo'    (https://github.com/newren/git-filter-repo/) instead. See the filter-branch manual page for more details; to squelch this warning, set FILTER_BRANCH_SQUELCH_WARNING=1.
Proceeding with filter-branch...
```

どうやら、gitでの仕様は推奨されていないようです。  
'git filter-repo'を代わりに使いなさいという警告が表示されます。  
待っていると、勝手に実行が始まります。  
気になる方は、Ctrl-Cで実行を止めた方が良いです。  

実行すると、HEADまでのすべてのコミット内容を確認し、ユーザー名とメールアドレスを変更します。  
変更完了後、再び以下のコマンドで確認します。  

``` shell
git log --pretty=full
```

## コミット

``` shell
git push -f
```

内容の反映時は、「-f」を付けてプッシュする必要があるらしい。  

## 更新後、githubのコミット履歴ページを確認
Commitsを確認すると、間違っていたユーザー情報が消え、更新後のユーザー情報のみとなっていることが確認出来ます。  


## git filter-repo を使って変更する場合

[https://nthcolor.net/posts/2022/02/03/](https://nthcolor.net/posts/2022/02/03/)  
試していないが、この方のブログのように行うと良い様子。  
