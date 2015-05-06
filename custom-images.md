# KVM用自作マシンイメージ

[TOC]

## 登録方法

マシンイメージを登録するには`vdc-manage`コマンドを使い、2つのリソースを登録します。`backupobject`と`image`です。デモ環境のマシンイメージを例に登録時に実行されているコマンドを見てみます。

参考例：

> ```
> backupobject add --storage-id=bkst-demo1 --uuid=bo-centos1d64 --display-name='centos-6.6.x86_64.kvm.md.raw.tar.gz' --object-key=centos-6.6.x86_64.kvm.md.raw.tar.gz --size=4294967296 --allocation-size=412668243 --checksum=bbdaa6193d5823e772c0df9260007b47 --container-format=tgz --description='centos-6.6.x86_64.kvm.md.raw.tar.gz'
> image add local bo-centos1d64 --account-id a-shpoolxx --uuid wmi-centos1d64 --arch x86_64 --description 'centos-6.6.x86_64.kvm.md.raw.tar.gz local' --file-format raw --root-device label:root --service-type std --is-public --display-name 'centos1d64' --is-cacheable
> image features wmi-centos1d64 --virtio
> ```

1. backupobject登録
2. 登録したbackupobjectを指定し、image登録
3. 登録したimageにオプション指定する必要があれば、featuresコマンドでオプションを定義

backupobjectとimageがペアになってる事が良く分かります。imageを登録する為の前提条件がbackupobjectである事も良く分かります。backupobjectが存在しない場合はimageを登録出来ません。imageは、backupobjectを説明する為のメタ情報です。

## rawファイルの生成方法

TODO

## rawファイルをtar.gzで圧縮

rawファイルのsparce領域を維持する為に、tarコマンドを組み合せてgzip圧縮します。

```
$ tar zScvpf example.raw.tar.gz example.raw
```

## マシンイメージ登録用パラメタ取得

+ `checksum`
+ `alloc_size`
+ `size`

### checksum

```
$ md5sum example.raw.tar.gz
```

実行結果例：

> ```
> $ md5sum example.raw.tar.gz
> 995c489d88e304b8bd07ea01a1b61b37  example.raw.tar.gz
> ```

`995c489d88e304b8bd07ea01a1b61b37`が`checksum`です。

### alloc_size

```
$ stat -c '%s' example.raw.tar.gz
```

実行結果例：

> ```
> $ stat -c '%s' example.raw.tar.gz
> 792946438
> ```

`792946438`が`alloc_size`です。

### size

```
$ tar -tvf example.raw.tar.gz
```

実行結果例：

> ```
> $ tar -tvf example.raw.tar.gz
> -rw-r--r-- root/root 4294967296 2015-04-21 19:07 example.raw
> ```

`4294967296`が`size`です。

### 値を整理

+ `checksum`: `995c489d88e304b8bd07ea01a1b61b37`
+ `alloc_size`: `792946438`
+ `size`: `4294967296`
