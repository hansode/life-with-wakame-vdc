# wakame-init

[TOC]

# wakame-init for rhel6

3つのファイルで構成されています。

1. `/etc/wakame-init`
2. `/etc/rc.d/init.d/wakame-init`
3. `/etc/default/wakame-init`

## `/etc/wakame-init`

2つの処理をします。

1. ネットワーク設定(ifcfgファイルの生成)
2. 公開鍵のインストール

## `/etc/rc.d/init.d/wakame-init`

SysV init用のinitスクリプトで、システム起動時に`/etc/wakame-init`を自動起動する為です。

```
chkconfig: 2345 9 91
```

## `/etc/default/wakame-init`

`wakame-init`はパラメタ指定する仕組みを備えています。

初期内容：

```
## wakame-init params
#USER=root
#IPV6INIT=no
#IPV6_AUTOCONF=no
#METADATA_LOCATION=drive
```

パラメタの内容：

+ `USER=root`: SSH公開鍵をインストールするユーザー名を指定します
+ `IPV6INIT=no`: (普段使いません)
+ `IPV6_AUTOCONF=no`: (普段使いません)
+ `METADATA_LOCATION=drive`: (普段使いません)
