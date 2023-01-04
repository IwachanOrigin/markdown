
# CreateProcess vs TabBrowser

## Introduction

    CreateProcess関数を使ってGoogle ChromeやMicrosoft Edgeなどのタブブラウザを起動しようとした格闘の記憶です。  
    まだ、戦いは継続しています(2023/01/04現在)  

## 背景

    WebView2を利用した簡易ブラウザソフトウェアがあるのですが、顧客はIEモードじゃなきゃ動かない！と騒ぎ立てていました。  
    残念なことに、WebView2ではIEモードをサポートしていません。  
[Microsoft Edge と WebView2 の違い](https://learn.microsoft.com/ja-jp/microsoft-edge/webview2/concepts/browser-features)  
![image](https://user-images.githubusercontent.com/12496951/210588182-246d625b-c869-48d2-b39c-610bf5259c4c.png)  

    そうなるとEdgeやChromeに頼ろうぜとなるわけです。  
    Win32APIにはCreateProcess関数やOpenProcess関数などがあり、ハンドルを取得してウィンドウに対してメッセージを送ったり出来ます。  
[プロセスの作成](https://learn.microsoft.com/ja-jp/windows/win32/procthread/creating-processes)  
[OpenProcess](https://learn.microsoft.com/ja-jp/windows/win32/api/processthreadsapi/nf-processthreadsapi-openprocess)  

    このあたりを利用したソフトウェアも持っていたので「コイツを使ったら良いんじゃない？」と提案したわけです。  
    で、最初のうちはそれで「行ける！」となったのですが、使っているうちに「プログラムを終了しても、ブラウザが閉じない」などと申しており。  
    そんなはずあるか！と思い立ち、調べていくことにしました。  

## どういうときに起きるんだ？

    まずは、自分の目で現象を確認するところからです。  
    検証には「Google Chrome」を主に使用しました。  
    CreateProcess関数経由で「Google Chrome」のフルパスを指定して起動します。  
    1つ目は問題なく起動、終了も出来ました。  
    続いて、1つ目を起動後、2つ目も起動します。  
    そのまま終了させると、2つ目のみ残ってしまいました。  
    では、手動でブラウザを先に起動しておき、1つ目、2つ目と起動します。今度はどちらも残ってしまいました。  
    ハンドルの取得に失敗してるのか？と漠然とした推測を立てました。  

## Process IDはどうなってるんだ？

    なんだかすぐにこのトピックに来たような文章ですが、このトピックに来るまでにどこの処理まで出来ているのか？なども調査していました。  
    紆余曲折合ってここまでなんとかたどり着いたわけです。  
    
    そもそもの大前提として、Process IDが想定と異なっている場合、どうしようもありません。  
    ということで、デバッグコードを仕込んでProcessIDを調べていきました。  
    CreateProcess関数で作成されたProcessIDを出力した後、Windows上で動いているすべてのウィンドウ情報を取得し、chrome.exeでフィルタしました。  
    この確認を行う際、chrome.exeのプロセスは1つも無い状態にしました。  
    1つ目  
> process id = (18216)  
> chrome.exe (18216)  
> chrome.exe (18216)  
> chrome.exe (22896)  

    あーいるいる。2つ存在するのは謎ですが、今はそこは問題ではありません。  
    では2つ目を起動します。  
    
    2つ目  
> process id = (29432)  
> chrome.exe (18216)  
> chrome.exe (22896)  
> chrome.exe (18216)  
> chrome.exe (22896)  

    29432、いないですねぇ。  
    いないもの...Another...夜見山北中...私は赤沢さん、好きです。  
    
    Microsoftさんの公式ドキュメントによると、プロセスIDは以下のように記載されています。  
[PROCESS_INFORMATION構造体](https://learn.microsoft.com/ja-jp/windows/win32/api/processthreadsapi/ns-processthreadsapi-process_information)  
![image](https://user-images.githubusercontent.com/12496951/210595120-5a88322c-c7b9-4a87-b6e9-ec6bcfaa5e8e.png)  

    クローズまで生きているんじゃないの？  

## 先人は居るんである

    私と同じ疑問にぶち当たった方がstackoverflowにいらっしゃいました。  
[CreateProcess returns handle different than launched Chrome.exe](https://stackoverflow.com/questions/54644803/createprocess-returns-handle-different-than-launched-chrome-exe)  

> The Chrome process you are spawning with CreateProcess() is, in turn, spawning its own child process(es) and then terminating itself.  
    
    なるほど！ でもそれだと私、困る！  

    ここからは推測ですが、モダンなブラウザはCreateProcess関数で作成した場合でも一つのプロセスIDに収束するような処理を  
    やってるんじゃないかなと思います。  
    頭の良い人たちはよくこんなの思いつくなーって私は感心してしまいます。  
    
    さて、これからどうするか。  
    回答者さんはこのようなアドバイスをくれてました。  
    
> You can get notified about the child PID(s) that Chrome itself spawns.  

    つまり、子プロセスの情報を手に入れるしか無いってことですね。  
    そして、私はまた辿り着いたわけです。Raymond Chen氏のブログ「The Old New Thing」に。  
[How do I wait until all processes in a job have exited?](https://devblogs.microsoft.com/oldnewthing/20130405-00/?p=4743)  

## 更新したら書く

    ここからは未知の領域です。これからまた、時間を見て試行する日々となるでしょう。  
    to be continued!  

