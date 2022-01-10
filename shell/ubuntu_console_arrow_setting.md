
# Ubuntu Console Arrow Setting

## Overview

   自宅や会社で普段から使用しているコンソールをナウでヤングな感じにしたいなって思いググるとPowerlineというものがヒットします。  
   「こいつを使えば、俺だって...!」と導入してはみるのですが、導入前と比べるとだいぶもっさりした感じになり、結局は消してしまっていました。  
   改めて考えてみると「俺って面食いだから、面構えだけそれっぽく出来れば満足するくね？」と思い立ち、コンソールの表示のみをナウでヤングにします。  


## Enviroment

   OS : Windows10 pro  
   Software : WSL2(Ubuntu 20.04)  


## Result

   こんな感じの見た目になります。  


## How to make

   Bashの表示部分を書き換えてナウでヤングにします。  

### ブランチ名表示の準備

    git でブランチ名が出てないと不安になるので、ブランチ名を出すためのスクリプトを公式より入手します。  

    cd ~ && curl https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh -o ".git-prompt.sh" && chmod +x .git-prompt.sh  

    配置はこれで完了です。  

### Powerline Glyph を表示するフォントの準備

    矢印っぽい表示を行うためにはPowerline Glyphに対応したフォントを使う必要があります。  
    ここではMicrosoft社より提供されている Cascadia Code PL を使用します。  
    Cascade Code はMicrosoft社の github にて公開されています。ダウンロードして対象のフォントをインストールしましょう。  

    https://docs.microsoft.com/ja-jp/windows/terminal/cascadia-code

    ダウンロードするといっぱいファイルが見つかりますが、以下を入れておけばここでやりたいことは満たせます。  
    <任意ディレクトリ>/CascadiaCode-xxxx.xx/ttf/CascadiaCodePL.ttf  

### bashrc の修正

    ここから見た目の変更を行っていきます。  
    変更するのは「.bashrc」です。  
    結論から先に書くと、以下記載の修正後のような記述にすればResultのようなナウでヤングな見た目になります。  

    修正前  
    if [ "$color_prompt" = yes ]; then  
       PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '  
    else  
       PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '  
    fi  
    unset color_prompt force_color_prompt  

    修正後  
    #  
    source ~/.git-prompt.sh  
    if [ "$color_prompt" = yes ]; then  
        #    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '  
        #(文字黒/背景灰色)日時 (文字灰色/背景水色) (文字黒)ディレクトリフルパス(文字水色/背景色デフォルト) ()  
        PS1='\[\e[30;47m\]\D{%y/%m/%d %H:%M:%S}\[\e[37;46m\]\[\e[30m\]\w\[\e[36;41m\]\[\e[0m\]$(__git_ps1 "\[\e[30;41m\](%s)")\[\e[31;49m\]\[\e[0m\] '  
    else  
        #PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '  
        PS1='\[\e[30;47m\]\D{%y/%m/%d %H:%M:%S}\[\e[37;46m\]\[\e[30m\]\w\[\e[36;41m\]\[\e[0m\]$(__git_ps1 "\[\e[30;41m\](%s)")\[\e[31;49m\]\[\e[0m\] '  
    fi  
    unset color_prompt force_color_prompt  

    「.bashrc」の59行目あたりでしょうか。  
    表示部分を謎の呪文で書き換えていますね。  
    一つ一つの意味を紐解いていくことでもっとナウでヤングなコンソールを作る土台の知識としてみたいと思います。  

#### PS1

     Bashの環境変数だそうで、ここで表示内容を定義しているようです。  
     つまりこいつをぐしゃぐしゃにしてしまえば表示内容もぐしゃぐしゃになるということですね。  
     コンソール上で  

     export PS1='Hello Prompt! '  

     と試しに入力して実行すると表示が変わります。  
     こいつがすべての秘密結社を操る大首領だとわかります。  

#### 変数

     謎の呪文を解析していきます。  
     まずは表示内容を定義している変数みたいなやつから。  

     |  変数  |  意味  | |  例  |
     | ---- | ---- | ---- |
     |  \h  |  ホスト名  |  iwa-pc  |
     |  \u  |  ユーザ名  |  iwa  |
     |  \w  |  ディレクトリ(フルパス)  |  /mnt/c/Users/iwa/  |
     |  \W  |  ディレクトリ  |  iwa  |
     |  \t  |  時間(24時間形式)  |  22:30:15  |
     |  \T  |  時間(12時間形式)  |  10:30:15  |
     |  \@  |  AM/PM  |  AM  |
     |  \d  |  日付  |  Mon Jun 10  |
     |  \D  |  日時(\D{%y/%m/%d %H:%M:%S})  |  22/01/09 01:59:26  |
     |  \#  |  コマンドの番号(1, 2, 3...)  |  1  |
     |  \n  |  改行  |  |
     |  \$  |  一般ユーザは$, rootは#  |  $  |

     表示の定義はこのようになっているみたいです。  
     これだけ見ると類推できるものもあるのでわかりやすいですね。  


#### 文字装飾

     項目「変数」の内容を装飾することでナウでヤングな表示が可能となります。  
     そして、PS1に定義した内容がぐしゃぐしゃーってなっている理由でもあります。  
     解析していきましょう。

     







