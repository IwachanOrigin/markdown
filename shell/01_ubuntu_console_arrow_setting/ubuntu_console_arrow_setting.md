
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
   ![console](https://user-images.githubusercontent.com/12496951/149662381-4fd077a4-7c70-4ddf-b659-9a1de5803130.PNG)

## How to make

   Bashの表示部分を書き換えてナウでヤングにします。  

### ブランチ名表示の準備

    git でブランチ名が出てないと不安になるので、ブランチ名を出すためのスクリプトを公式より入手します。  

```sh
cd ~ && curl https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh -o ".git-prompt.sh" && chmod +x .git-prompt.sh  
```
    配置はこれで完了です。  

### Powerline Glyph を表示するフォントの準備

    矢印っぽい表示を行うためにはPowerline Glyphに対応したフォントを使う必要があります。  
    ここではMicrosoft社より提供されている Cascadia Code PL を使用します。  
    Cascade Code はMicrosoft社の github にて公開されています。ダウンロードして対象のフォントをインストールしましょう。  

    https://docs.microsoft.com/ja-jp/windows/terminal/cascadia-code

    ダウンロードするといっぱいファイルが見つかりますが、以下を入れておけばここでやりたいことは満たせます。  
    <任意ディレクトリ>/CascadiaCode-xxxx.xx/ttf/CascadiaCodePL.ttf  


### Windows Terminalでの設定

    インストールしたフォントをWindows Terminalの画面で使用するように設定を変更します。  
    [設定] > [プロファイル] > [既定値] > [フォント フェイス] と進み、「Cascade Code PL」を選択し、右下の[保存]を押下します。  
    既定値を変更しても変わらない場合、変更されたいプログラム(Ubuntuなど)の設定も一緒に変更します。  
    Ubuntuの場合、[追加の設定] > [外観] と移動することでフォントの設定が表示されます。  


### bashrc の修正

    ここから見た目の変更を行っていきます。  
    変更するのは「.bashrc」です。  
    結論から先に書くと、以下記載の修正後のような記述にすればResultのようなナウでヤングな見た目になります。  

##### 修正前
```sh
if [ "$color_prompt" = yes ]; then  
   PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '  
else  
    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '  
fi  
unset color_prompt force_color_prompt
```

##### 修正後
```sh
#  
source ~/.git-prompt.sh  
if [ "$color_prompt" = yes ]; then  
    #PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '  
    #(文字黒/背景灰色)日時 (文字灰色/背景水色) (文字黒)ディレクトリフルパス(文字水色/背景色デフォルト) ()  
    PS1='\[\e[30;47m\]\D{%y/%m/%d %H:%M:%S}\[\e[37;46m\]\[\e[30m\]\w\[\e[36;41m\]\[\e[0m\]$(__git_ps1 "\[\e[30;41m\](%s)")\[\e[31;49m\]\[\e[0m\] '  
else  
    #PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '  
    PS1='\[\e[30;47m\]\D{%y/%m/%d %H:%M:%S}\[\e[37;46m\]\[\e[30m\]\w\[\e[36;41m\]\[\e[0m\]$(__git_ps1 "\[\e[30;41m\](%s)")\[\e[31;49m\]\[\e[0m\] '  
fi  
unset color_prompt force_color_prompt  
```
    「.bashrc」の59行目あたりでしょうか。  
    表示部分を謎の呪文で書き換えていますね。  
    一つ一つの意味を紐解いていくことでもっとナウでヤングなコンソールを作る土台の知識としてみたいと思います。  

#### PS1

     Bashの環境変数だそうで、ここで表示内容を定義しているようです。  
     つまりこいつをぐしゃぐしゃにしてしまえば表示内容もぐしゃぐしゃになるということですね。  
     コンソール上で  
```sh
export PS1='Hello Prompt! '  
```
     と試しに入力して実行すると表示が変わります。  
     こいつがすべての秘密結社を操る大首領だとわかります。  

#### 変数

     謎の呪文を解析していきます。  
     まずは表示内容を定義している変数みたいなやつから。  

|  変数  |  意味  |  例  |
---|---|---
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

     '\[' と '\]' で囲むことでその後ろの文字列が加工されます。「エスケープシーケンス」と呼ぶらしいです。  
     参考ページによると、'\[' と '\]' で囲まなくても加工自体は出来るそうですが、操作を行った際に表示がおかしくなることがあるのできちんと囲むのが吉です。  
     '\e[' はASCIIのエスケープ文字を使うことを宣言する特殊文字です。この後ろに装飾パターン文字列を記載することで設定できます。  
```sh
PS1='\[\e[1m\] 太字 \[\e[0m\] 普通'  
```
     上述例では '1m' を指定することで「太字」にしています。  
     その後ろで '0m' を指定することで「普通」にしています。  
     この例からわかるように '\[' と '\]' で囲まれた文字装飾の影響範囲は次の '\[' と '\]' で囲まれた範囲までです。  
     ただし、後述の「文字色・背景色」でわかるのですが、同じ項目のみの場合に限ります。  
     異なる項目を設定する場合、ひとつ前に設定した装飾は引き継がれてしまうので注意しましょう。  

     装飾には以下表のような種類があります。  

|  定義  |  意味  |
--- | --- 
|  0m  | 装飾無し  |
|  1m  |  太字  |
|  2m  |  細字(未対応らしい)  |
|  3m  |  イタリック体  |
|  4m  |  下線  |

#### 文字色・背景色

     文字の色や背景色を変更します。  
```sh
PS1='\[\e[31m\] 文字色赤 \[\e[0m\] 何もなし  \[\e[41m\] 背景色赤 \[\e[0m\] 何もなし'  
```
     この例では文字色赤・背景色赤を試しています。  
     どうやら、「文字色」を変更後に「背景色」を変更した場合、文字色は前の設定を引き継いでしまうようです。  
     ここでは間に「何もなし」を挟んでいますが、挟まない場合は「背景色赤」という文字列の色は「赤」になります。  
     つまり、文字列が見えなくなります。  

     文字色と背景色で主に利用されそうなものは以下になります。  

##### 文字色
|  定義  |  意味  |
--- | ---
|  30m  |  黒  |
|  31m  |  赤  |
|  32m  |  緑  |
|  33m  |  黄  |
|  34m  |  青  |
|  35m  |  紫  |
|  36m  |  水  |
|  37m  |  灰  |

     '39m' で文字色をデフォルト値に戻せます。  

##### 背景色
|  定義  |  意味  |
--- | ---
|  40m  |  黒  |
|  41m  |  赤  |
|  42m  |  緑  |
|  43m  |  黄  |
|  44m  |  青  |
|  45m  |  紫  |
|  46m  |  水  |
|  47m  |  灰  |

     '49m' で背景色をデフォルト値に戻せます。  

     他にも細かい文字色や背景色の設定ができるようです。  

#### 装飾の同時設定

     今までの装飾を一つにまとめて設定できないとイケメンにできないですよね。  
     ということでまとめて設定するのですが、この場合に使用するのは「;」です。  
     装飾設定を「;」で区切ることで上手に設定が出来ます。  
```sh
PS1='装飾無し \[\e[1;37;42m\] 太字、文字白、背景緑 \[\e[0m\] 装飾無し'  
```
     '1m', '37m', '42m' を「;」で区切ることで一気に設定しているわけです。  
     この時、最後の装飾値のみに「m」が必要で、その前の装飾値の「m」は省略できます。  

#### 謎の矢印について

     なんかかっこいい「>」って感じのやつは特殊文字を使って実現されているそうです。  
     ここでは「Cascade Code」を利用していますね。  
     インストールしておくことで、「」が文字化けせずに利用できるようになります。  

### 参考ページ

[Qiitaのページ](https://qiita.com/hmmrjn/items/60d2a64c9e5bf7c0fe60)
    
### 終わり

    これでイケメンが出来上がったはずです。  


