
# write elisp

## Explanation

    elispの書き方をメモしていく場所です。  

## setq

    グローバル変数を定義するらしい。  

## 一時的にload-pathを追加する方法

    load-pathに「~/.emacs.d/test」を追加する方法です。  

``` elisp
(setq load-path (cons "~/.emacs.d/test" load-path))
```

## let

    ローカル変数を定義するらしい。  
    書き方が少し特殊です。  

``` elisp
(let ((value "test")))
```

    このように記述しないと、valueという変数名に値をセット出来ません。  
    一つ前の括弧は無駄では？と思いますが、複数宣言する場合にも使うので無駄ではないっぽいです。  
    また、生存権はlet評価中の括弧内の間だけです。括弧の外では利用できません。  

``` elisp
(setq x "global")
(message "%s" x)
=>global

(let ((x "value"))
  (message "%s" x))
=>value

(message "%s" x)
=>global
```

    グローバル変数を一時的に変更することも可能です。letを抜けると、元の値に戻ります。  
    複数の変数定義時、変数は同時に値をセットします。そのため、一つ前の変数の値を利用した初期化などでは  
    うまくいかない場合があるようです。  

``` elisp
(setq x "global")
=>global

(let ((x "value") (y x))
  (message "x= %s y=%s" x y))
=>"x= value y=global"
```

    となります。'x' には初期値として"global"をセットしていました。  
    ここで、letを用いて'x'には"value"をセットし、'y'にも同じ値をセットしようとします。  
    しかし、実際に代入される値は"global"です。  
    つまり、letにおける'x'の評価と'y'の評価は同時に行われています。  

## let*

    letの亜種です。使い方は基本的に同じのようです。  
    ただ、letとは違い、複数の変数を定義した場合、順番に値がセットされます。  

``` elisp
(setq x "global")
=>global

(let* ((x "value") (y x))
  (message "x= %s y=%s" x y))
=>"x= value y=value"
```

    となります。 let同様、'x' には初期値として"global"をセットしていました。  
    処理的にはすべて同じ書き方になりますが、出力結果は'y'も"value"です。  
    このことから、let*では先に'x'への代入が評価され、その後で'y'の代入が評価されます。  

## elispで自作関数を呼び出す方法  

    括弧「()」で関数を括る必要があります。  
    例えば、「elisp-test」という関数を定義している場合、(elisp-test)と括ることで評価してくれます。  
    






