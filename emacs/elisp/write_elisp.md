
# write elisp

## Explanation

    elispの書き方をメモしていく場所です。  

## setq

    グローバル変数を定義するらしい。  

## 一時的にload-pathを追加する方法

    load-pathに「~/.emacs.d/test」を追加する方法です。  

``` elisp
(setq load-path(cons "~/.emacs.d/test" load-path))
```

## let

    ローカル変数を定義するらしい。

## elispで自作関数を呼び出す方法  

    括弧「()」で関数を括る必要があります。  
    例えば、「elisp-test」という関数を定義している場合、(elisp-test)と括ることで評価してくれます。  
    






