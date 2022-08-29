
# JNI Table

## 紹介

    JNIを使うとき、謎の文字列(Ljava/lang/Stringみたいなやつ)を使っていることがあります。  
    これは、C++側でJava(Android)のオブジェクトを召喚して使うときに利用する型宣言みたいなものです。  
    この宣言する方法を「メソッドのシグニチャー」と呼ぶらしいです。  
    初見だと「は？」ってなるし、絶対忘れるので読み方をまとめておきます。  
    
## 環境

    Qt : 5.15.2

## メソッドのシグニチャー

    "()V"とか"(Ljava/lang/String;)Ljava/lang/String;"みたいなやつらのことです。  
    記述方法は以下のようになります。  

    (引数のシグニチャー)返り型のシグニチャー  

    なので、上述例の場合、以下を表しています。  
    
* ()V
: 引数なし、戻り値はVoid  
* (Ljava/lang/String;)Ljava/lang/String;
: 引数はjava/lang/Stringを1つ、戻り値もjava/lang/String  
    

## メソッドのシグニチャー例

    Javaコードと対比させた場合のメソッドのシグニチャー例を記載します。  
    
| No.  | java | メソッドのシグニチャー |
|:--|:--|:--|
| 1 | public void func() {}  | ()V  |
| 2 | public int getValue() {}  | ()I  |
| 3 | public void setValue(int value) {}  | (I)V  |
| 4 | public String changeText(String tex) {}  | (Ljava/lang/String;)Ljava/lang/String; |
| 5 | public String concat(String tex1, String tex2) {}  | (Ljava/lang/String;Ljava/lang/String;)Ljava/lang/String;  |

## 困ったときは

    Android公式とQt公式のドキュメントを読みましょう。  
