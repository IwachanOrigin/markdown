
# treesit

## 概要

各言語で書かれたプログラムのシンタックスハイライトを強化してくれます。  
emacs 29からはビルトインとして入っています。  

## 設定

``` elisp
;; tree-sitter
(when (and (fboundp 'treesit-available-p)
           (treesit-available-p))
  (setq treesit-font-lock-level 4))

(use-package treesit-auto
  :ensure t
  :defer 2
  :custom
  (treesit-auto-install 'prompt)
  :config
  (treesit-auto-add-to-auto-mode-alist 'all)
  (global-treesit-auto-mode))

```

パッケージ名が *tree-sitter* となるのは、外部パッケージです。  
elpaなどからダウンロードするような動きになります。  
  
ビルトインパッケージ名は *treesit* です。  
use-packageを利用してチェックすると、elpaなどに *treesit* が見つからないためエラーとなります。  
そのため、*treesit-available-p* で有効にしています。  
*treesit-font-lock-level* は、ハイライトするレベル設定のようです。最大値4を設定します。  
  
treesitの構文チェックで必要となるのはdllですが、従来は自分で導入したり設定が必要でした。  
その導入を簡単にするのが *treesit-auto* です。  
上述の設定を行うことで、見知らぬ拡張子をemacsで開くと自動的に登録されている  
Language Server のURLを探し、ダウンロード・ビルドを行うか確認してきます。  
ビルドして生成されたdllファイルは ~/.emacs.d/tree-sitter/ に格納されます。  

* Ref  
[Emacs 29 でTree-sitterを利用してシンタックスハイライトする](https://zenn.dev/hyakt/articles/42a1d237cdfa2a)  
[github_treesit-auto](https://github.com/renzmann/treesit-auto)  

 

