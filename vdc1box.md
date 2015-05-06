# Wakame-vdcデモ環境構築

[TOC]

## vdc1box環境の構築

Wakame-vdcのデモ用にvdc1boxと言う全部入り環境がvmdk形式で提供されています。vmdk形式は、VirtualBoxかVMware Playerで実行可能です。

ダウンロードURL：

+ [http://dlc.wakame.axsh.jp/demo/1box/vmdk/1box-openvz.netfilter.x86_64.vmdk.20150427171952gited56eff.zip](http://dlc.wakame.axsh.jp/demo/1box/vmdk/1box-openvz.netfilter.x86_64.vmdk.20150427171952gited56eff.zip) ※約2.0Gあります

詳しい設定に関しては、[Demo Image](http://wakameusersgroup.org/demo_image.html)を参考に構築して下さい。

## `~/.musselrc`の作成

vdc1box環境用に`~/.musselrc`を作成します。

```
$ vi ~/.musselrc
```

```
DCMGR_HOST=127.0.0.1
account_id=a-tutorial
```

以上で`mussel`を使う準備が整いました。
