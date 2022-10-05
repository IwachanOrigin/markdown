
# markdown output pdf

## Introduction

    markdown file を pdf file で出力する方法です。  

## Dependency

    wkhtmltopdf  

[wkhtmltopdf](https://wkhtmltopdf.org/)  

## Command

``` shell
pandoc.exe test.md -f markdown -t html5 -c https://cdn.jsdelivr.net/npm/github-markdown-css@3.0.1/github-markdown.css -o test.pdf
```

## Result

    markdownファイルと同じフォルダにpdfファイルが出力されます。  
