
# CreateProcess vs TabBrowser

## Introduction

    CreateProcess関数を使ってGoogle ChromeやMicrosoft Edgeなどのタブブラウザを起動しようとした格闘の記憶です。  
    結果的に、Google ChromeもMicrosoft Edgeも複数起動して制御できるようになりました。  
    Firefoxは未確認です。  

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
    紆余曲折あってここまでなんとかたどり着いたわけです。  
    
    そもそもの大前提として、Process IDが想定と異なっている場合、どうしようもありません。  
    ということで、デバッグコードを仕込んでProcessIDを調べていきました。  
    CreateProcess関数で作成されたProcessIDを出力した後、Windows上で動いているすべてのウィンドウ情報を取得し、Chrome.exeでフィルタしました。  
    この確認を行う際、Chrome.exeのプロセスは1つも無い状態にしました。  

    1つ目  
> process id = (18216)  
> Chrome.exe (18216)  
> Chrome.exe (18216)  
> Chrome.exe (22896)  

    あーいるいる。2つ存在するのは謎ですが、今はそこは問題ではありません。  
    では2つ目を起動します。  
    
    2つ目  
> process id = (29432)  
> Chrome.exe (18216)  
> Chrome.exe (22896)  
> Chrome.exe (18216)  
> Chrome.exe (22896)  

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

## 教えて、Raymond先生！

    広大なネットの海をさまよってたどり着いた桃源郷は、やはりMicrosoftさんのブログであったというわけです。  
    Joel on softwareの作者からブログに「レイモンド陣営」と書かれるほど、古からMicrosoftでWindows開発されている方のブログは勉強になります。  
    では早速、子プロセスの情報を手に入れてみましょう！！  
    まずは、「Chrome.exe」を0個起動した状態で、レイモンド先生のサンプルコードにPIDを出力したりする処理を加えて実行してみます。  

    タスクマネージャー

![image](https://user-images.githubusercontent.com/12496951/215512895-2ad29879-39a2-4611-9cf5-6ede00d0e1fa.png)

    コンソール

![image](https://user-images.githubusercontent.com/12496951/215512613-b1f6cb77-7389-4a39-853e-186c8b1b04c5.png)

    この場合、プロセスは終了しません。  
    コンソールに出ている「New PID: 24252」が最後に生成されたプロセスIDで生きている「Chrome.exe」のプロセスIDになります。  

## 話は聞かせてもらった！ Chrome.exeのプロセスはすべて消滅する！

    では、本命の「Chrome.exe」を1個起動した状態で、レイモンド先生のサンプルコードにPIDを出力したりする処理を加えて実行してみます。  
    タスクマネージャー

![image](https://user-images.githubusercontent.com/12496951/215514894-07b8450f-0d74-438c-9fd0-ae4a7746475a.png)

    コンソール

![image](https://user-images.githubusercontent.com/12496951/215515071-0cffc46e-b917-4faa-8b43-a060342da40e.png)

    「All done」じゃねーよ！！！！！ Chromeちゃん、すべてのプロセスをkillしないで！！！！  
    ここまで来ると、「Chrome」はどうやってプロセスを統合しているんだ...?と興味が湧いてきてしまいますね。  
    この時点で「もうChromeのコードを読んでみるしか無いのだろうか...」とあきらめムードでした。  

## 案外、絞り出せばアイデアは生まれるもの

    何かアイデア無いかなーと悩んでいたときにひらめきました。  
    CreateProcess関数でプロセスを作成する場合、実行ファイルのフルパスか、少なくとも実行ファイル名が必要になります。  
    ということは「XXXX.exe」のような実行ファイル名は必ず手に入るわけです。  
    プロセス名って基本的に「Chrome.exe」のようにexeファイルの名前となるルールがあります。  
    プロセスIDが見つからない場合、起動しているすべてのプロセスを調べて「XXXX.exe」でフィルタすれば良いのでは？というアイデアです。  

## キマシタワー

    先程の仮説は正しかったようです。「XXXX.exe」絞り込む技はとても奴らに効きました。  
    絞り込んでみると見つかったプロセスは8個...あれ、起動は4つしかしてないぜ？  
    「Chrome.exe」ちゃんはレンダリングウィンドウとその枠を持っているようで、いわゆる服を着ているような状態になっています。  
    どちらも制御できそうな風を装っているのですが、レンダリングウィンドウ側のウィンドウハンドルを制御しても枠は移動しません。  
    このレンダリングウィンドウは枠の子プロセスとなっていますから、枠を移動させると一緒に移動してくれます。  
    なので、ここでは「GetParent」関数を利用して子プロセスを持ってないウィンドウハンドルだけ抽出してしまいましょう。  
    こうして、欲しい4つのHWNDだけ手に入ります。  
    
    確認用のプログラムでコンソール出力

![image](https://user-images.githubusercontent.com/12496951/215520719-2c1dd874-e73e-4078-8352-91aaa82e2076.png)

    spy++
    
![image](https://user-images.githubusercontent.com/12496951/215520921-7454cd8a-9e53-48c7-9a67-9aa6ecb31c21.png)

## 次の課題はなんだろう？

    ここまでで、ある程度はプロセスを絞り込める手段が分かってきました。しかし、私に必要なのは汎用的にプロセスを制御出来る仕組みです。  
    例え、タブブラウザを一つ制御できても、新たなプロセスを立ち上げた場合に同じウィンドウを制御しては意味がありません。  
    ということは、すべてのプロセスを調べる際にすでに「制御中だぞ」と認識できるフラグのようなものが必要になります。  
    「CreateProcess」関数を利用するソフトウェア側がすべてのプロセスを管理している代物であればHWNDのチェックで判断出来たでしょう。  
    しかし、私のプログラムは非常に疎結合な作りになっているのでソフトウェア側で管理することが難しかったのです。  
    ということは、「起動したプロセス側に何らかのフラグが必要になる」ということになります。  
    普通、そんなの無いよねー。  
    
## あるんだな、これが！

[teratailのQA](https://teratail.com/questions/322782)

![image](https://user-images.githubusercontent.com/12496951/215523279-c0bc018b-642f-4383-a607-804ad8ae2fd9.png)

[ms learn](https://learn.microsoft.com/ja-jp/windows/win32/winmsg/using-window-properties)

    teratailの有識者さんの過去回答で教えていただきました。ありがとうございます！  
    回答にも記載がある通り、ポインタにプロパティ値として無理やり書き込む部分もあるので、何でも詰め込むのは良くないでしょう。  
    動作確認をした限り、タブブラウザ自身の動作に問題は無さそうでしたので、オリジナルなプロパティをセットして判断することにしました。  
    ここまでで、プロセスを0個起動した状態でタブブラウザを制御する仕組みは想定通りに動くようになりました。  

## 最後の難題

    最後の課題。それは「ユーザーが起動したChrome.exe」なのか「ソフトウェアが起動したChrome.exe」なのかを判定する手段です。  
    これがわからないとユーザーが既に起動していたChrome.exeを誤って制御してしまう可能性があります。  
    正直、運用でカバーしてもらうことも可能だと思いますが、ここまで来たら汎用的に利用できるソフトウェアを作りたいですよね？  
    ソフトウェアエンジニアならみんなそうだと思っています！！ ちょうどアイデアも思いついていたので試したかったのもありますね。  

## プロセス開始時間で判断すれば良い...はずだった

    「CreateProcess」関数を実行するプログラムの起動時間と「Chrome.exe」の起動時間を比較して、古ければユーザーが起動したプロセスと  
    判断すれば行ける！ それが私のアイデアでした。確かに起動プロセス数が0個のときはうまくいきました。  
    
    しかし、起動プロセス数が1個場合、新たに起動したプロセスは「すでに終了している」という扱いでした。  
    なぜなら、「Chrome.exe」のプロセスはすでに起動しているプロセスに収束するのですべて過去のプロセスとして扱われるのです。  
    なんてこったい！！！！  

## ならば、HWNDの生成時間でどうだ！？！？！

[CodeProjectのQA](https://www.codeproject.com/Questions/1078412/How-to-get-the-start-time-of-the-window-with-hwnd)

    私のような普通の人が思いつくアイデアなんて出尽くしているわけで、「そんなものはない」と回答されていました。  
    ぬわーっ！！ どうしたらいいんだーっ！？！？！？  

## 再びのひらめきと力技

    どうすっかなぁと考えながらソフトウェアをいじっていたときに気付きとひらめきが起きました。  
    そういえば、タブブラウザって新たに起動すると前回作ったウィンドウよりも前に出てくるなぁって気付いたのです。  
    そりゃ最前面に出てこないと制御できなから当たり前だろうって話ですが、この習性を上手く活用出来ないか？と思ったわけです。  
    つまり、「CreateProcess」関数を実行した際、最も最前面に近い「Chrome.exe」は起動したプロセスとニアイコールになります！  
    
    もちろん、前提としてウィンドウを生成している最中や対象プロセスを探している最中に別の「Chrome.exe」プロセスを最前面に  
    持ってこられると誤ったウィンドウを取得してしまう可能性があります。  
    しかし、チェック処理なんて1~2秒掛かるか否かくらいなので、そこまで意地悪されたらもうこのソフトウェアは捨てて  
    別のソフトウェアを使ってくださいが良いでしょう。私ではそこまで完璧になれません。  
    
## Z-Indexをすべて取得するんだ！！

    ということで、Z-Indexをすべて取得して比較が必要になります。  
    残念ながら、Z-Indexを取得するWin32APIは2023年現在も存在しないので、対象のHWNDがどの場所にいるかをループで探していきます。  
    見つかったら保持しておいて、最後にどっちが前にいる？ってのを取得すれば最前面のウィンドウがわかるって寸法ですね。  

## 今のところ上手く動いているな

    色々と悩みましたが、結果的には「Google Chrome」も「Microsoft Edge」も想定通りに動いてくれています。  
    (「Firefox」では動作確認していません。)  
    また少し、Win32APIに詳しくなってしまいましたがプログラミングの海は広いのでもっと簡単に対応する術があったかもしれません。  
    まだまだ勉強しないといけませんね。  

## 終わり！

