# Wakame-vdcのセキュリティグループ

[TOC]

# セキュリティグループ実装

Wakame-vdcのセキュリティグループはnetfilterで実装しています。

+ `iptables`
+ `ebtables`

# netfilterのデバッグ

開発時や調査時は、netfilterと対話話する必要があります。その際には3つのコマンドを用います。

1. `iptables`
2. `iptables-save`
3. `ebtables`
4. `tcpdump`

コマンドとオプションについて説明して行きます。

# iptables

## ルールの表示

適用されているルールを表示するには`-L`オプションを指定します。

```
$ sudo iptablse -L
```

## 名前解決の無効化

`-L`オプションだけ指定すると、iptablesコマンドは名前解決を試みます。名前解決不可能なIPアドレスが指定されていると、リストが表示されるまでに時間がかかります。名前解決が有効である事は、邪魔である事が大半です。iptablesは名前解決を無効化する`-n`オプションを用意しています。

```
$ sudo iptables -n -L
```

`-n`と`-L`とまとめて扱う事も可能です。

```
$ sudo iptables -nL
```

個人的にはこちらを好んで使っています。以降の説明では`-nL`を用います。

## テーブルの指定

vdcセキュリティグループでは、iptablesが扱うテーブルのうち、2つを利用しています。

1. filter
2. nat

テーブルを指定するには、`-t <table>`オプションを使用します。

```
$ sudo iptables -nL -t <table>
```

filterテーブルの場合：

```
$ sudo iptables -nL -t <table>
```

natテーブルの場合：

```
$ sudo iptables -nL -t <table>
```

なお、`-t`オプションを指定しない場合、`-t filter`として扱われます。

# iptables-save

`iptables -nL`によるテーブル指定表示は便利ですが、複数テーブルを同時に表示する事は出来ません。複数テーブルをまとめて扱うコマンド`iptables-save`が容易されています。

## iptables-saveによる複数テーブル横断したリスト表示

コマンド名とコマンドの振る舞いは異なります。saveとなっていますが、実際の振る舞いはdumpです。実行すると標準出力にルールが出力されます。

```
$ sudo iptables-save
```

## パケットカウント

```
$ sudo iptables-save -c
```

# ebtables

## ルールの表示

適用されているルールを表示するには`-L`オプションを指定します。なお、iptablesと違い名前解決は常に無効化されています。

```
$ sudo ebtables -L
```

## 生成コマンドセットを表示

`-L`オプションではチェインごとに表示されます。これには一長一短があります。時には、どの様なコマンドセットで生成されたのかを確認したい時があります。ebtablesは`-L`オプションを拡張するオプションを用意しています。コマンドセットを出力する拡張オプションは`--Lx`オプションです。

```
$ sudo ebtables -L --Lx
```

## パケットカウント

```
$ sudo ebtables -L --Lc
```

## コマンドセットとパケットカウントを同時に扱う

```
$ sudo ebtables -L --Lc --Lx
```

## テーブルの指定

vdcセキュリティグループでは、iptablesが扱うテーブルのうち、2つを利用しています。

1. filter
2. nat

# tcpdump

```
$ sudo tcpdump -s 0 -i <interface>
```
