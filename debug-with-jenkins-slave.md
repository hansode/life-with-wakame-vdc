# Jenkins-Slaveを使ったデバッグ 

[TOC]

Jenkinsが実施したスモークテスト環境におけるデバッグ手順です。

# 事前準備

## Slaveにログイン

```
$ ssh <jenkins-slave>
```

## `jenkins`アカウントにスイッチ

```
$ sudo su - jenkins
```

## ジョブ用フォルダへ移動

```
$ cd /var/lib/jenkins/workspace/<*ジョブ名*>
```

## `MUSSEL_RC`の設定

```
$ ls -l MUSSEL_RC
$ cat MUSSEL_RC
$ export MUSSEL_RC=`pwd`/MUSSEL_RC
```

# テストを実施

## 再実行したいテストフォルダへ移動

```
$ cd client/mussel/test/acceptance/v12.03/<*カテゴリ名*>
```

## 再実行したいテストスクリプトを実行

```
$ time ./t.<*実施内容*>.sh
```
