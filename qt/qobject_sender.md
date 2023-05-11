
# QObject Sender Method

## このメソッドは何？

    QObjectにはsenderメソッドが存在します。  
    このメソッドは、直前にシグナルを発報したオブジェクトを取得するメソッドです。  
    例えば、以下のようなコードが存在するとします。  

``` c++
QPushButton* button = new QPushButton(this);
QObject::connect(button, &QPushButton::pressed, this, &MyWidget::slotButtonPressed);
```

    このような処理の場合にsenderメソッドを呼び出すと「QPushButtton」のオブジェクトを取得することができます。  
    ただし、senderメソッドはQObjectのポインタを返却します。  
    実際にQPushButtonとして利用するにはqobject_castによるキャストが必要です。  
    以下のような感じになります。  

``` c++
auto btn = qobject_cast<QPushButton*>(this->sender());
```

## 使いどころは？

    静的なGUIでの実装を行う場合、直前にシグナルを発報したオブジェクトはよっぽどのことが限りは  
    不要だと思います。  
    重宝するのは、動的にオブジェクトを生成・解放して利用する場合や自作のプロパティから値を取得する場合、  
    setDataメソッドで値を設定し、利用する場合です。  
    QActionにsetDataメソッドを使用してファイルパスを埋め込み、その値を取得する場合を例にします。  

``` c++
QMenu* menu = new QMenu(this);
QAction* action = menu->addAction(name);
QObject::connect(action, &QAction::triggered, this, [this, action]() {
  this->slotTriggerdAction();
  });
QString path = "/my/directory/path";
action->setData("path");
...

...
void MyWidget::slotButtonPressed(const QString& path)
{
  auto action = qobject_cast<QAction*>(this->sender());
  if (action)
  {
    QString path = action->data().toString();
    qDebug() << "path = " << path;
  }
}
```

    このように、QActionとして取得し、設定したパスを取得、利用することが出来ます。  
    dataメソッドはQVariant型を返すので、設定を行った型に変換して利用する必要があります。  
    
