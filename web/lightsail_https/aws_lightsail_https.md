
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


### Create static ip


### Add firewall config


### Create distribution


## お名前どっとこむ

### DNS setting


## Lightsail(ssh setting)

### Install web server(ex. apache2)



### Install Java(ex. Java11)



## Result

    



