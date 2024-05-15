
# Connect Windows Side Emacs Server

## Introduction

    Windows側でサーバーとして起動しているemacs GUIへWSL2上のUbuntu 24.04からコマンドラインでファイルパスを送り、ファイルを開く方法です。  

## Config

    Ubuntu 24.04 の[.bashrc]へ以下の設定を行います。

``` bash
# Setup emacsclient path of windows side
EMACSCLIENT_PATH="/mnt/c/software/emacs/bin/emacsclientw.exe"

# Convert filepath func
function wslpath_to_winpath {
    wslpath -w "$1"
}

# Connect emacs server
function emacs {
    if [ -n "$DISPLAY" ]; then
        if [ -f "$1" ]; then
            WIN_PATH=$(wslpath_to_winpath "$1")
            "$EMACSCLIENT_PATH" -n "$WIN_PATH"
        else
            "$EMACSCLIENT_PATH" -n "$@"
        fi
    else
        if [ -f "$1" ]; then
            WIN_PATH=$(wslpath_to_winpath "$1")
            "$EMACSCLIENT_PATH" -nw "$WIN_PATH"
        else
            "$EMACSCLIENT_PATH" -nw "$@"
        fi
    fi
}

```



