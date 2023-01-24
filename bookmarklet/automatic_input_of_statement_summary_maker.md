
# 発言まとめメーカー自動入力スクリプト

## Introduction

    発言まとめメーカーは自動入力する仕組みが無く、手動で入力する必要があります。  
    とても素晴らしいサービスですが、いくつも手動で入力するのは辛いのでブックマークレットで入力できるようにしました。  

## Environment

    OS: Windows 10  
    ブラウザ: Microsoft Edge, Google Chrome  

## How to use

    以下のコードをワンラインにしてご使用ください。  

``` javascript
javascript:(
  function(){
    var ret=prompt("A,B,C...", "良いです良いです,なんなんやーっ！");
    let tags=ret.split(',');
    for (var i=0;i<tags.length;i++)
    {
      document.getElementsByClassName('MuiButtonBase-root MuiFab-root MuiFab-circular MuiFab-sizeMedium MuiFab-primary css-15w7hv2-MuiButtonBase-root-MuiFab-root')[0].click();
    }
    let cnt=0;
    let inputs = document.getElementsByClassName('MuiInput-input MuiInputBase-input MuiInputBase-inputMultiline css-66dh3a-MuiInputBase-input-MuiInput-input');
    for(var i=0;i<inputs.length;i++)
    {
      let input = inputs[i];
      if(input.getAttribute("aria-invalid"))
      {
        let lastValue = input.value;
        input.value=tags[cnt++];
        let event=new Event('input', { bubbles: true});
        event.simulated=true;
        let tracker=input._valueTracker;
        if(tracker){tracker.setValue(lastValue);}
        input.dispatchEvent(event);
        if(cnt>=tags.length){break;}
      }
    }
  }
)();
```

    または、Gistにもアップロードしていますのでこちらもご利用ください。  

[automatic_input_of_statement_summary_maker.js](https://gist.github.com/IwachanOrigin/ec74ec5e71f08fb6c647792f58e35c73)

    実行方法は通常のブックマークレットと同じです。  
    実行時、入力プロンプトが表示されます。  
    カンマ区切り(StringA,StringB,StringC,...)にして値をご入力ください。  

![prompt](https://user-images.githubusercontent.com/12496951/214237339-3f8e1154-fcf1-49fb-aefc-febbeb428a96.png)


## やっていること

    1. 入力する言葉の最大値を取得し、その分のテキストエリアを生成する。  
    2. テキストエリアを全て取得する。  
    3. テキストエリアからaria-invalid属性を取得し、falseのものだけを保持する。  
    4. 発言を上から順に流し込んでいく。  

## 参考になったスニペット

[Trigger simulated input value change for React 16](https://github.com/facebook/react/issues/11488#issuecomment-347775628)  

    マジ神すぎるスニペットです。このコードのおかげで作れました。  

