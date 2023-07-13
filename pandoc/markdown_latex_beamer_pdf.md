
# markdown latex beamer pdf

## Introduction

    markdown fileをLatex経由でbeamer形式のpdfで出力する方法です。  

## Dependency

    latex  
    pandoc  

[Latex windows](https://www.tug.org/texlive/acquire-netinstall.html)  
[pandoc-latex-template](https://github.com/Wandmalfarbe/pandoc-latex-template)  
[pandoc](https://pandoc.org/index.html)  

## Install

    Dependencyに記述した3ソフトウェアをインストールしてください。  
    それぞれのインストール方法に沿って頂ければ大丈夫だと思います。  
    簡単に書くと、以下の通りです。  
    - Latex
      - インストーラ経由でインストールしてください。1時間くらいかかる場合があります。  
    - Pandoc
      - ダウンロードしたバイナリを任意のフォルダへ配置し、PATHに登録してください。  
    - Pandoc-latex-template
      - Pandocの設定ファイルフォルダへ、ダウンロードしたLatexのファイル「eisvogel.latex」を配置してください  
      - pandocの設定フォルダは「pandoc.exe --version」で確認することが出来ます。  
      - たぶん「%AppData%/pandoc/templates」です。  

## Command

``` shell
pandoc.exe input.md -o output.pdf --from markdown --to beamer --template eisvogel.latex --listings --pdf-engine "xelatex" -V CJKmainfont="Meiryo UI"
```

### About arguments

|  引数  |  意味  |  例  |
---|---|---
|  -o  |  出力ファイル名  |  -o output.pdf  |
|  --from  |  元の形式?  |  --from input.md  |
|  --to  |  変換の形式?  |  --to beamer  |
|  --template  |  利用するテンプレート  |  --template eisvogel.latex  |
|  --listings  |  ?  |  --listings  |
|  --pdf-engine  |  PDF生成に利用するエンジン. windowsはxelatexを設定しないとだめかも  |  --pdf-engine "xelatex"  |
|  --V  |  言語の変更?  |  --V  |
|  CJkmainfont  |  フォントの変更。日本語が表示できるフォントを設定しないとpandocでエラー  |  CJKmainfont="Meiryo UI"  |

## Result

    markdownファイルと同じフォルダにbeamer形式のスライドと化したpdfファイルが出力されます。  
