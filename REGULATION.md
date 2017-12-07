## 概要
ISUCONの詳細を記載します。

## 参加事項
1チームは1-3人とします。
リモートでの助っ人は一応認めておりますが、グローバルに公開されてるインスタンスなので常識の範囲内でお願いします。

## チューニング可能な時間について
12/8 13:00 ~ 12/9 13:00 となります。
なお、12/9 11:00~ で他チームのスコアを見えなくする予定です。（そっちのほうがドキドキ感あるので）

## 運営のサポートについて
松山までお問い合わせください。
基本的な質問やトラブルなどにはできる限り対応いたしますが、
スコアに影響するような質問にはお答えできませんのでご了承ください。

## サーバへの接続について

key: 全インスタンス `isucon-shared-key`
port: 全インスンタンス `443`
command: ssh -i ~/.ssh/isucon-shared-key.pem centos@${IP} -p 443

サーバーIPと秘密鍵はお渡しします。


## アプリケーションサーバ構成
### OS
CentOS 7.2

### アプリケーション
参考実装としてRuby, Node.js, Go, PHP, Java のWebアプリケーションが用意されています。
ただし各々の初期性能は一致していません。どれをベースに用いても構いません。独自で実装してもOKです。

### Nginx
WebサーバとしてデフォルトでNginxが起動しており、systemdで管理されています。
こちらももちろん変えていただいて構いません。

### MariaDB
ポート3306で MariaDB が起動しています。

ユーザ名: `root`
パスワード: なし

## アプリケーション起動
初期設定ではNode実装のアプリケーションが動いています。
ブラウザから80番ポートにアクセスして動作確認しましょう。

## アプリケーションへのログインについて
こちらの名前の名前がID、名前のアルファベットをそれぞれ後ろにひとつづつずらしていったものがPWとなります。

例
```
ID Asao
PW Btbp
```

## 言語の切り替え
参考実装実装として、Node.js, Ruby, Go, PHP, Javaが用意されています。（最初はnode実装が動いています。）

各言語実装は systemd で管理されています。基本的には `systemctl stop/start` ならびに `systemctl enable/disable` で制御します。
また、 `Ruby`, `Go`, `Java` に関しては二つのserviceを起動させる必要があります。
`PHP`, `Node.js` のserviceは一つです。

以下に起動したり停止したりするsampleコマンドを記載しておきます。

### Ruby
#### service 自動起動設定->起動
```bash
$ sudo systemctl enable isucon-ruby-isutomo.service
$ sudo systemctl enable isucon-ruby-isuwitter.service
$ sudo systemctl start isucon-ruby-isutomo.service
$ sudo systemctl start isucon-ruby-isuwitter.service
```
#### service 停止->自動起動解除
```bash
$ sudo systemctl stop isucon-ruby-isutomo.service
$ sudo systemctl stop isucon-ruby-isuwitter.service
$ sudo systemctl disable isucon-ruby-isutomo.service
$ sudo systemctl disable isucon-ruby-isuwitter.service
```

### Node.js
#### service 自動起動設定->起動
```bash
$ sudo systemctl enable isucon-node.service
$ sudo systemctl start isucon-node.service
```
#### service 停止->自動起動解除
```bash
$ sudo systemctl stop isucon-node.service
$ sudo systemctl disable isucon-node.service
```

### Java
#### service 自動起動設定->起動
```bash
$ sudo systemctl enable isucon-java-isutomo.service
$ sudo systemctl enable isucon-java-isuwitter.service
$ sudo systemctl start isucon-java-isutomo.service
$ sudo systemctl start isucon-java-isuwitter.service
```
#### service 停止->自動起動解除
```bash
$ sudo systemctl stop isucon-java-isutomo.service
$ sudo systemctl stop isucon-java-isuwitter.service
$ sudo systemctl disable isucon-java-isutomo.service
$ sudo systemctl disable isucon-java-isuwitter.service
```

### PHP
#### service 自動起動設定->起動
```bash
$ sudo systemctl enable isucon-php.service
$ sudo systemctl start isucon-php.service
```
#### service 停止->自動起動解除
```bash
$ sudo systemctl stop isucon-php.service
$ sudo systemctl disable isucon-php.service
```
### Go
#### service 自動起動設定->起動
```bash
$ sudo systemctl enable isucon-go-isutomo.service
$ sudo systemctl enable isucon-go-isuwitter.service
$ sudo systemctl start isucon-go-isutomo.service
$ sudo systemctl start isucon-go-isuwitter.service
```

#### service 停止->自動起動解除
```bash
$ sudo systemctl stop isucon-go-isutomo.service
$ sudo systemctl stop isucon-go-isuwitter.service
$ sudo systemctl disable isucon-go-isutomo.service
$ sudo systemctl disable isucon-go-isuwitter.service
```


#### node実装からgo実装に切り替える例が以下です。

```bash
$ sudo systemctl stop isucon-node
$ sudo systemctl disable isucon-node
$ sudo systemctl enable isucon-go-isutomo.service
$ sudo systemctl enable isucon-go-isuwitter.service
$ sudo systemctl start isucon-go-isutomo.service
$ sudo systemctl start isucon-go-isuwitter.service
```

### PHPの場合
systemd での切り替えとは別に、nginxの設定変更が必要です。
php 用の `/etc/nginx/php-fpm.conf` というファイルがあるのでそれを使用してください。

```bash
$ sudo systemctl start isucon-php
$ sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
$ sudo cp /etc/nginx/php-fpm.conf /etc/nginx/nginx.conf
$ sudo nginx -t -c /etc/nginx/nginx.conf
$ sudo systemctl restart nginx
```

## ベンチマーク実行
ポータルサイトから実行できます。
ポータルのURL: http://13.115.144.95

まずは一度ベンチマークを実行してみましょう。

## ルール詳細
指定されたサーバー上のアプリケーションのチューニングをおこない、それに対する計測ツールのスコアで競技をおこないます。
与えられた1台のサーバーのみでアプリケーションの動作が可能であればどのような変更を加えてもOKです。

計測ツールとブラウザの挙動に差異がある場合、計測ツールの挙動を正とします。
また初期実装毎に若干の挙動の違いはありますが、計測ツールに影響のない挙動に関しては問題ないもの（仕様）として扱います。

なお、以下の機能は変更してはいけません。
- URI（ポート, HTTPリクエストパス）
- レスポンスのDOM構造
- JavaScript/CSSファイルの内容

### スコアについて
基本スコアは以下のルールで算出されます。
```
成功レスポンス数(GET) x 1 + 成功レスポンス数(POST) x 2 - (サーバエラー(error)レスポンス数 x 10 + リクエスト失敗(exception)数 x 20 + 遅延POSTレスポンス数 x 100)
```
ただし、基本スコアと計測ツールの出すスコアが異なっている場合は、計測ツールの出すスコアが優先されます。

### 減点対象
以下の事象が発生すると減点になります

- 存在するべきDOM要素がレスポンスHTMLに存在しない
- リクエスト失敗（通信エラー等）が発生する
- サーバエラー(Status 5xx)・クライアントエラー(Status 4xx)
- 他、計測ツールが検出したケース

### 注意事項
- リダイレクトはリダイレクト先が正常なレスポンスを返せた場合のみ、1回レスポンスが成功したと判断します
- POSTリクエストの失敗は大幅な減点対象です

### 制約事項
以下の事象が発生すると点数が無効になります
- GET /initialize へのレスポンスが10秒以上かかる
- JavaScript/CSSへのアクセスがエラーになる
