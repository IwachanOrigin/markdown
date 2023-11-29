
# aws Lightsail https

## Introduction

    AWS Lightsailを使用し、https化してJavaサービスを動かす手法です。  
    デフォルトドメインの場合、証明書が何処にあるか不明となってしまうので  
    Let's Encrypt を使用して証明書を取得します。  
    そのため、独自ドメインをお名前ドットコムで取得しています。  

## Dependency

    AWS Lightsail  
    Java 8.0(11.0)  
    Ubuntu 22.04  

## Contract

    AWS Lightsail を利用する設定を行います。  
    契約してLightsailコンソールへアクセスできるようにしてください。  


## Lightsail(AWS Console) 

### Create instance

1. ホーム画面にアクセスし、「インスタンスの作成」を選択します。  

![lightsail_home.png](./images/lightsail_home.png)  

2. リージョンを作成する場所を選択します。金額には影響が無いようなので、「東京」を選択します。  

![lightsail_createinstance_1.png](./images/lightsail_createinstance_1.png)  

3. インスタンスイメージを設定します。プラットフォーム、設計図の選択は図のように選択します。  

![lightsail_createinstance_2.png](./images/lightsail_createinstance_2.png)  

4. オプションは特に設定を行いません。  

![lightsail_createinstance_3.png](./images/lightsail_createinstance_3.png)  

5. プランの選択です。ここでは最も安いプランを選択します。  

![lightsail_createinstance_4.png](./images/lightsail_createinstance_4.png)  

6. インスタンス名を設定します。一意でわかりやすい名前にしましょう。ここではデフォルト名のままにしています。  

![lightsail_createinstance_5.png](./images/lightsail_createinstance_5.png)  

7. 無事、インスタンスが作成されました。黒塗りつぶし箇所はIPアドレスが表示されます。  

![lightsail_createinstance_6.png](./images/lightsail_createinstance_6.png)  


### Create static ip


### Add firewall config


### Create distribution


## お名前どっとこむ

### DNS setting


## Lightsail(ssh setting)

### Install web server(ex. apache2)



### Install Java(ex. Java11)



## Result

    



