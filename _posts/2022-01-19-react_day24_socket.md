---
layout: post
title: "デーモン、socketについて" 
date: 2022-01-19 
category: read 
excerpt: ""
---

## やったこと
フロントの最終課題とハッカソン準備

朝活のマスタリングTCP/IPをやってるときに出てきた「デーモン」と「ソケット」がわからなかったので調べる　　

### デーモンって何？？
**DAEMON(デーモン)とは、コンピューターのメモリに常駐し、サーバーのプログラムやシステムの要求に応じて自動的にサービスを提供するバックグランドシステム**  
特にUNIX系などのマルチタイプOSのバックグラウンドプロセスとして動作し、httpd や cron など様々なサービスを提供する   

#### httpd
AWSの環境構築時にApacheを使用した際、よく打ってたコマンド  
これもデーモンらしい  

httpdとは、主にUNIX系OSのWebサーバソフトでよく見られる実行ファイル名およびコマンド名。
Web上のデータ伝送に用いるHTTP（Hypertext Transport Protocol）を扱うデーモン（daemon）という意味。  

一度起動されると常にメモリ上に留まって要求に応じて動作する常駐プログラム（デーモン）の一種で、  
外部からのHTTP接続を受け付けて要求されたファイルを送信したり、送られたデータを受け取って保存したり、指定されたプログラムを起動したりする  


### ソケットって何？？
TCP/IPでの通信で利用される、コンピュータが持つネットワーク内でのIPアドレスと、  
IPアドレスのサブアドレスであるポート番号を組み合わせたネットワークアドレスのこと  
ソケットは、通信を行なうアプリケーションソフトがTCP/IPを扱うためのネットワーク用APIのことも指す  

[知ったかぶりをしていたソケット通信の基礎を改めて学んでみる](https://qiita.com/megadreams14/items/32a3eed4661e55419e1c)  

#### ソケット通信とは  
> インターネットはTCP/IPと呼ぶ通信プロトコルを利用しますが、そのTCP/IPをプログラムから利用するには、プログラムの世界とTCP/IPの世界を結ぶ特別な出入り口が必要となります。 その出入り口となるのがソケット(Socket)であり、TCP/IPのプログラミング上の大きな特徴となっています。
#### ソケットを使うと何が出来るの？  
HTTPよりも下のレイヤを扱うことが出来きる  
ソケットを使うとTCP/UDP上で動く（トランスポート層レベル）アプリケーションを開発することが出来るため、  
HTTPよりもよりコンパクトな通信を使ってデータを送信したいといったことも可能となる  

サーバ側のライフサイクル
- create ソケットの作成
- bind ソケットを特定のIPアドレスとポートに紐付け
- listen 接続の待受を開始
- accept 接続を受信
- close 接続を切断

クライアント側のライフサイクル
- create ソケットの作成
- bind ソケットを特定のIPアドレスとポートに紐付け
- connect リモートソケットに接続
- close 接続を切断

### そしたらWebSocketって何？？
[今さら聞けないWebSocket~WebSocketとは~](https://qiita.com/chihiro/items/9d280704c6eff8603389)  
Webにおいて双方向通信を低コストで行うための仕組み。プロトコルの一種。  
SNSの登場からよりリアルタイムなやりとりが求められるようになってきた  

#### HTTPのボトルネック
- 1つのコネクションで1つのリクエストしか送ることができない
- リクエストはクライアントからしか送ることができない
  - サーバーから通信を始めることができない
- ヘッダーが冗長
  - 情報量が多くなってしまう

そこでwebsocketが登場した  

#### WebSocketプロトコル
もともとはHTML5の仕様の一部として策定されていたが、現在は単独のプロトコルとして進められている。  
WebSocketプロトコルは、サーバとクライアントが一度コネクションを確立すると、その後の通信を専用プロトコルで行う。  
- サーバプッシュ機能
  - 一度コネクションを確立したあとは、サーバとクライアントのどちらからも通信を行うことが可能
- 通信量削減
  - 一度コネクションを確立するとそのコネクション上で通信を行う(HTTPのように、通信のたびにコネクションを作らない)
  - ヘッダのサイズが最小2byte、最大14byteと小さい

#### 通信の仕組み①　-ハンドシェイク-
ハンドシェイクはHTTP通信によって行われる。

リクエスト  
HTTPのUpgradeヘッダを使用し、プロトコルの変更を行う。

レスポンス  
ステータスコード101「Switching Protocols」が返る。  
ハンドシェイクによってコネクションが確立し、これ以降はHTTPではなくWebSocketのプロトコルで通信が行われることになる。

#### 通信の仕組み②　-双方向通信の実現-
ハンドシェイクによってコネクションが確立すると、双方向通信をWebSocketプロトコルで行うことができるようになる。  
送信データはフレームという単位で扱われる。  


## frontの課題
### 要素を右寄せしたい！！！
ずっとflexやtext-alignばっかりいじっていたが、marginの指定が正解だった  
[CSSで要素を右寄せする方法の全て｜文字も画像も右寄せにしよう](https://www.sejuku.net/blog/72034)  
```css
margin: 0 0 0 auto;
```
結論、これが正解  
