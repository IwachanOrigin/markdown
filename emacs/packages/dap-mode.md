
# dap-mode

## Introduction

    dap-modeを導入し、デバッグ実行を行うための設定・導入についてです。  

## Environment

    OS: Windows 10 Pro  
    emacs: 29.0.60  
    llvm: 16.0.0  

## Setting

### 1. llvm のインストール

    デバッグに利用するlldbを取得するため、llvmをインストールします。  
    以下のgithubページより、Releaseからインストーラをダウンロードします。  

[llvm-project](https://github.com/llvm/llvm-project)

    インストール先は任意ですが、init.elにデバッグに使用するバイナリファイルの  
    パスを指定する必要がありますのでご注意ください。  

### 2. init.elの設定

    init.elに以下の記述を行い、パッケージをインストールします。  
    ここでは「use-package」を用いてインストールしています。  

``` lisp
(use-package dap-mode
  :defer 2
  :config
  ())
```

