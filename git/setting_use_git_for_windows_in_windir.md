
# setting use git for windows in windows directories

## Introduction

    Windows TerminalにてUbuntuを使いながらgitの操作をしている場合、/mnt/以下のフォルダ上で操作を行うと  
    git status等を行うとものすごく動作が重たくなる場合があります。  
    これは以下の条件で発生するようです。
    
- 巨大なリポジトリ
- git が Ubuntu 側にインストールされている
- リポジトリのディレクトリはWindows側(/mntの方)にある
    
    Ubuntu側のgitを使われてしまうと遅くなるので、Windows側のgitを利用して処理速度を改善します。  
    
## Environment

    OS: Windows 10(Ubuntu 22.04)  

## 設定方法

    .bashrcに以下のスクリプトを追加します。  
    
``` shell
# checks to see if we are in a windows or linux dir
function isWinDir {
    case $PWD/ in
        /*planio/*) return $(true);;
        /*github/*) return $(true);;
        *) return $(false);;
    esac
}

# wrap the git command to either run windows git or linux
function git {
    if isWinDir
    then
        git.exe "$@"
    else
        /usr/bin/git "$@"
    fi
}
```

## scriptの解説

### function git

    git というコマンドを受け取ったら、isWinDir を実行してディレクトリを確認します。  
    結果がtrueの場合、windows側のディレクトリであると判断し、git.exeを利用します。  
    falseの場合、Ubuntu側のディレクトリであると判断します。  

### function isWinDir

    isWinDir では PWDコマンドで現在のフォルダを取得し、その中に「planio」や「github」があれば  
    trueを返します。それ以外の場合はfalseを返します。  

## 注意点

    git.exeを実行していますが、これはWindows側のPATHにgit for windowsが登録されていないといけません。  
    PATHへの登録を行わない場合、フルパスを記載してください。  

