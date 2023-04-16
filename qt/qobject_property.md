
# QObject Property

## このメソッドは何？

    Property, setPropertyメソッドは、ユーザー側でオブジェクトに値を設定する手段です。  
    setPropertyでキーと値を設定し、Propertyにキーを与えて取得することが出来ます。  
    例えば、以下のようなコードが存在するとします。  

``` c++
QPushButton* button = new QPushButton(this);
QObject::connect(button, &QPushButton::pressed, this, &MyWidget::slotButtonPressed);
QString path = "/my/path";
button->setProperty("path", path);
```

    この場合、以下のようにしてプロパティ値を取得することが出来ます。  

``` c++
auto path = button->property("path").toString();
```

    propertyメソッドはQVariant型を返却するので、想定している型に変換して利用する必要があります。  

## 使いどころは？

    オブジェクトに設定したいけど、要件に合う格納先が無い場合に重宝します。  
    今回使用した例だとsetTextもありますが、setTextだとボタン自体に表示される文字も更新されます。  
    ボタンの文字は変更しない、または別の文字を表示しながらファイルパスなどの異なる情報を埋め込み、  
    ボタンクリック時にPropertyメソッドで値を取得し、利用することが出来ます。  

