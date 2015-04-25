# 標準イメージ

[TOC]

# マシンイメージの種類

通常3つのマシンイメージが提供されています。

1. `wmi-centos1d64`: 通常はこちら
2. `wmi-haproxy1d64`: ロードバランサーアプライアンス用
3. `wmi-lbnode64`: ロードバランサーテスト用イメージ

# アカウント

アカウントは2つ存在しています。

1. `root`
2. `wakame-vdc`

| ユーザー名 | パスワード   | 用途               |
|:-----------|:-------------|:-------------------|
| root       | (ロック状態) | SSHログイン        |
| wakame-vdc | wakame-vdc   | コンソールログイン |

`wakame-vdc`は通常使いません。主に開発用途です。何らかの理由によりインスタンスにSSHログイン出来ない時に、コンソールからログインしてインスタンスの状態や設定を調査するのに使われます。

# マシンイメージの中身

## `wmi-centos1d64`

CentOS-6.6の最小構成にWakame-vdcインスタンスとして必要なパッケージを幾つかインストールしています。

インストールパッケージ：

+ `openssh`: SSHログインする用
+ `wakame-init`: SSH公開鍵のインストールと、インスタンス固有ネットワーク設定をします

他のマシンイメージの基礎となるので、他のマシンイメージには必ずインストールされています。

## `wmi-haproxy1d64`

ロードバランサーアプライアンス用です。アプライアンス用に必要なパッケージがインストールされています。

インストールパッケージ：

+ `haproxy`
+ `stud`
+ `amqpclient`

## `wmi-lbnode64`

ロードバランサーアプライアンスのテスト用に特化したアプライアンスと言っても過言ではありません。テストに必要なパッケージがインストールされています。

インストールパッケージ：

+ `nginx`
+ `fcgiwrap`

主にHTTP連携テスト用途であるため、特定パスにHTTPリクエストすると、インスタンス固有情報などが返って来ます。

テストに特化したPATH：

+ `/`: インスタンスのUUID
+ `/cgi-bin/env.cgi`: インスタンスの環境変数。`env`コマンドの実行結果を得られます。
+ `/cgi-bin/sleep.cgi`: 600秒間sleepします。

### `GET /`

実行結果例：

> ```
> $ curl http://10.0.2.101/
> i-cmr0tcgt
> ```

HTTPリクエスト先が、どのインスタンスなのかを確認する為に使われます。

### `GET /cgi-bin/env.cgi`

実行結果例：

> ```
> $ curl http://10.0.2.101/cgi-bin/env.cgi
> CONSOLETYPE=serial
> CONTENT_LENGTH=
> CONTENT_TYPE=
> DOCUMENT_ROOT=/usr/share/nginx/html
> DOCUMENT_URI=/cgi-bin/env.cgi
> FCGI_ROLE=RESPONDER
> GATEWAY_INTERFACE=CGI/1.1
> HTTP_ACCEPT=*/*
> HTTP_HOST=10.0.2.101
> HTTP_USER_AGENT=curl/7.19.7 (x86_64-redhat-linux-gnu) libcurl/7.19.7 NSS/3.13.6.0 zlib/1.2.3 libidn/1.18 libssh2/1.4.2
> PATH=/sbin:/usr/sbin:/bin:/usr/bin
> PREVLEVEL=N
> PWD=/usr/share/nginx/html/cgi-bin
> QUERY_STRING=
> REDIRECT_STATUS=200
> REMOTE_ADDR=10.0.2.15
> REMOTE_PORT=41412
> REQUEST_METHOD=GET
> REQUEST_URI=/cgi-bin/env.cgi
> RUNLEVEL=3
> SCRIPT_NAME=/cgi-bin/env.cgi
> SERVER_ADDR=10.0.2.101
> SERVER_NAME=_
> SERVER_PORT=80
> SERVER_PROTOCOL=HTTP/1.1
> SERVER_SOFTWARE=nginx/1.0.15
> SHLVL=4
> TERM=linux
> UPSTART_EVENTS=runlevel
> UPSTART_INSTANCE=
> UPSTART_JOB=rc
> _=/bin/env
> previous=N
> runlevel=3
> ```

ロードバランサー連携前後の`REMOTE_ADDR`値の変化を確認する為などに使われています。

### `GET /cgi-bin/sleep.cgi`

実行結果例：

> ```
> $ curl http://10.0.2.101/cgi-bin/sleep.cgi
> ^C
> ```

意図的に停止しない限り、600秒経過するまで接続を維持したままです。
ロードバランサーのアルゴリズムが正しく機能しているかどうかを確認する為に使われています。
