
# OpenSSH Client/Server

## Introduction

    Window10(Build 1809)以降、OpenSSHをOSレベルで利用できるようになったようです。  
    ここでは、SSH Client(Windows10)からSSH Server(Windows10)の接続方法をまとめておきます。  

## Reference

以下のMicrosoft 公式ドキュメントの内容そのままなのでそっち見ても良いかも。  

[https://learn.microsoft.com/ja-jp/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui](https://learn.microsoft.com/ja-jp/windows-server/administration/openssh/openssh_install_firstuse?tabs=gui)

## Install

インストール手順を以下に示します。

1. [設定] - [システム] - [オプション機能] を選択します。

2. [追加された機能]が中央に出てくるので以下の項目がインストールされているかを確認します。
   - OpenSSH クライアント
   - OpenSSH サーバー

3. 何れか追加されていない場合、[機能の追加]を選択します。

4. [オプション機能を追加する]で以下項目にチェックを付けて[追加]を選択します。
   - OpenSSH クライアント
   - OpenSSH サーバー

5. 追加が始まります。追加されたら「再起動が必要です」と出るので、再起動します。

6. 再起動後、OpenSSHサーバーを利用する場合に限り、[サービス]を起動します。
   Winキー - メニュー上で[service]と入力 - [サービス]が見つかると思うので選択します。

7. [OpenSSH SSH Server]を探します。

8. 起動してないと思うので、起動します。
   現在の設定は手動起動になっていますので、自動起動が必要な場合は[プロパティ]から自動起動の設定を行います。

## Connect

SSHサーバーへの接続手順を以下に示します。
接続時、ログインパスワードが必要ですので設定しておきます。

1. クライアントPCでPowerShellを起動します。

2. 以下は接続コマンドの例です。

``` shell
ssh domain\username@servername
```

パット見よくわかりませんが、ローカルサーバーなら以下のコマンドで接続できます。

``` shell
ssh <username>@<ip address>
```

<username>にはPCのユーザー名を、<ip address>にはPCのIPアドレスを指定してください。
アクティブディレクトリなどが設定されている場合、domain nameなども必要かも知れません。


3. 接続すると、以下のような出力がコンソールに表示されます。「yes」と答えます。

``` shell
The authenticity of host 'servername (10.00.00.001)' can't be established.
ECDSA key fingerprint is SHA256:(<a large string>).
Are you sure you want to continue connecting (yes/no)?
```

4. PCのパスワード入力を求められるので入力します。
パスワードに誤りが無ければ、接続に成功すると思います。
コンソールには

``` shell
<username>@<PC Name> <DirectoryPath>:
```
みたいな内容が表示されると思います。

5. 接続を終了する場合はexitなどで終了します。
