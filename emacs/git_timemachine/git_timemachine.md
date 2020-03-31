
# git-timemachine

## 概要

    emacs melpa package.
    emacs上でコミットされたコードの履歴を追うことが出来る。
    
## 追加方法

    iwachanOriginの「.emacs.d」リポジトリをcloneする。
    または、melpaパッケージからインストールし、use-packageで利用するようにする。
    [git-timemachine](https://gitlab.com/pidu/git-timemachine)
    
## Usage

    - run : M-x git-timemachine
    - p   : Visit previous historic version
    - n   : Visit next historic version
    - w   : Copy the abbreviated hash of the current historic version.
    - W   : Copy the full hash of the current historic version.
    - g   : Goto nth revision.
    - t   : Goto revision by selected commit message.
    - q   : Exit the time machine.
    - b   : Run magit-blame on currently visited revision.
    - c   : Show current commit using magit.

## Options

    - Set `git-timemachine-abbreviation-length` (default 12) to your preferred length for abbreviated commit hashes.
    - Set `git-timemachine-show-minibuffer-details` (default t) to control whether details of the commit are shown in the minibuffer.
    - Also M-x customize git-timemachien.
