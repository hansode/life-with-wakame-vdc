# Mussel: Wakame-vdc API Client

[TOC]

# はじめに

本書では、musselを通じてWakame-vdcの基本的なAPI呼び出しを中心に、インスタンスとロードバランサに関する基本的で実践的な操作を体験出来ます。

# musselとは？

musselは、Wakame-vdcのリソースを直感的に扱う事を目的に設計・実装されたWakame-vdc APIクライアントです。

Wakame-vdcのAPIは、Web APIです。つまり、curlコマンドで簡単に呼び出せます。簡単に呼び出せはしますが、どうしても設定・手続きが幾つか必要です。どう言う事かは、curlコマンドでインスタンス作成する場合を見てみます。

curlによるAPI呼び出し例：

```
$ curl -fsSkL \
 -H X_VDC_ACCOUNT_UUID:a-shpoolxx \
 -X POST \
 --data-urlencode cpu_cores=1 \
 --data-urlencode hypervisor=openvz \
 --data-urlencode image_id=wmi-lbnode1d64 \
 --data-urlencode memory_size=256 \
 --data-urlencode ssh_key_id=ssh-ruekc3bs \
 --data-urlencode vifs@vifs.json \
 http://127.0.0.1:9001/api/12.03/instances.yml
```

HTTP通信においては、十分シンプルです。しかし、「Wakame-vdcインスタンスを作成する」と言う面においては、どうでしょうか？幾つかのHTTP特有の手続き・設定・指定が必要です。musselは、「Wakame-vdcインスタンスを作成する」と言う命令において、本質的ではない設定・手続きを吸収し、Wakame-vdcのリソースを直感的に扱える様にしています。それでは、musselでインスタンス作成をする場合を見てみましょう。

musselによるAPI呼び出し例：

```
$ mussel instance create \
 --cpu-cores   1 \
 --hypervisor  openvz \
 --image-id    wmi-lbnode1d64 \
 --memory-size 256 \
 --ssh-key-id  ssh-ruekc3bs \
 --vifs        vifs.json
```

curl版とmussel版を比較すると、どうでしょうか？mussel版では「Wakame-vdcインスタンスを作成する」為の命令・パラメタだけが、顔を覗かせています。ユーザーはHTTPの手続きを気にする事なく、Wakame-vdcのリソース操作に注力出来ます。

musselによる快適かつ直感的なWakame-vdcリソース操作をお楽しみ下さい。

# musselのインストール方法

Wakame-vdcは`/opt/axsh/wakame-vdc`にインストールされる事を想定しています。以下に2つの方法を紹介します。

## Yumによるインストール(RHEL6互換環境)

Wakame-vdcはYumリポジトリを提供しているので、Yumによるインストールが推奨されています。

```
$ sudo curl -fsSkL \
 https://raw.githubusercontent.com/axsh/wakame-vdc/master/rpmbuild/wakame-vdc.repo \
 -o /etc/yum.repos.d/wakame-vdc.repo
$ sudo yum install -y wakame-vdc-client-mussel
```

実行結果例：

> ```
> $ sudo curl -fsSkL \
> >  https://raw.githubusercontent.com/axsh/wakame-vdc/master/rpmbuild/wakame-vdc.repo \
> >  -o /etc/yum.repos.d/wakame-vdc.repo
> $ sudo yum install -y wakame-vdc-client-mussel
> Loaded plugins: fastestmirror
> Loading mirror speeds from cached hostfile
> Setting up Install Process
> Resolving Dependencies
> --> Running transaction check
> ---> Package wakame-vdc-client-mussel.noarch 0:15.03-20150427171952gited56eff.el6 will be installed
> --> Finished Dependency Resolution
>
> Dependencies Resolved
>
> ================================================================================
>  Package       Arch   Version                            Repository        Size
> ================================================================================
> Installing:
>  wakame-vdc-client-mussel
>                noarch 15.03-20150427171952gited56eff.el6 wakame-vdc-rhel6  92 k
>
> Transaction Summary
> ================================================================================
> Install       1 Package(s)
>
> Total download size: 92 k
> Installed size: 376 k
> Downloading Packages:
> wakame-vdc-client-mussel-15.03-20150427171952gited56eff. |  92 kB     00:00
> Running rpm_check_debug
> Running Transaction Test
> Transaction Test Succeeded
> Running Transaction
>   Installing : wakame-vdc-client-mussel-15.03-20150427171952gited56eff.el   1/1
>   Verifying  : wakame-vdc-client-mussel-15.03-20150427171952gited56eff.el   1/1
>
> Installed:
>   wakame-vdc-client-mussel.noarch 0:15.03-20150427171952gited56eff.el6
>
> Complete!
> ```

## Gitによるインストール

musselはシェルスクリプトで実装されているのでコンパイル不要です。つまり、ソースツリーから簡単にインストールが可能です。

```
$ git clone https://github.com/axsh/wakame-vdc.git
$ sudo mkdir -p /opt/axsh
$ sudo mv wakame-vdc /opt/axsh
$ sudo ln -s /opt/axsh/wakame-vdc/client/mussel/bin/mussel /usr/bin/mussel
```

実行結果例：

> ```
> $ git clone https://github.com/axsh/wakame-vdc.git
> Initialized empty Git repository in /home/centos/wakame-vdc/.git/
> remote: Counting objects: 74797, done.
> remote: Compressing objects: 100% (269/269), done.
> remote: Total 74797 (delta 137), reused 1 (delta 1), pack-reused 74505
> Receiving objects: 100% (74797/74797), 25.77 MiB | 9.22 MiB/s, done.
> Resolving deltas: 100% (47748/47748), done.
> $ sudo mkdir -p /opt/axsh
> $ sudo mv -i wakame-vdc /opt/axsh
> $ sudo ln -s /opt/axsh/wakame-vdc/client/mussel/bin/mussel /usr/bin/mussel
> ```

# musselの使い方

## 実行方法

musselの使い方を簡単に説明します。musselは、引数を2つ以上必要とします。第1引数にはリソースを、第2引数にはコマンドを指定します。必要に応じてオプションを指定します。

最小構成：

```
$ mussel <resource> <command>
```

オプション指定時：

```
$ mussel <resource> <command> --name value
$ mussel <resource> <command> --name value --name2 value2
```

## `mussel-completion.bash`による入力支援

musselではbashのcompletion(補完)機能を利用した入力支援機能を提供しています。以下に2つの方法を紹介します。

### `bash-completion`による設定(RHEL6互換環境)

`bash-completion`と連携した使い方がお勧めです。`bash-completion`をインストールするには、EPELをインストールする必要があります。

```
$ sudo yum install -y epel-release
```

実行結果例：

> ```
> $ sudo yum install -y epel-release
> Loaded plugins: fastestmirror
> Setting up Install Process
> Loading mirror speeds from cached hostfile
>  * base: ftp.nara.wide.ad.jp
>  * extras: ftp.nara.wide.ad.jp
>  * updates: mirror.nus.edu.sg
> Resolving Dependencies
> --> Running transaction check
> ---> Package epel-release.noarch 0:6-8 will be installed
> --> Finished Dependency Resolution
>
> Dependencies Resolved
>
> ================================================================================
> Package                Arch             Version         Repository        Size
> ================================================================================
> Installing:
>  epel-release           noarch           6-8             extras            14 k
>
> Transaction Summary
> ================================================================================
> Install       1 Package(s)
>
> Total download size: 14 k
> Installed size: 22 k
> Downloading Packages:
> epel-release-6-8.noarch.rpm                              |  14 kB     00:00
> Running rpm_check_debug
> Running Transaction Test
> Transaction Test Succeeded
> Running Transaction
>   Installing : epel-release-6-8.noarch                                      1/1
>   Verifying  : epel-release-6-8.noarch                                      1/1
>
> Installed:
>   epel-release.noarch 0:6-8
>
> Complete!
> ```

次に`bash-completion`をインストールします。

```
$ sudo yum install -y bash-completion
```

> ```
> $ sudo yum install -y bash-completion
> Loaded plugins: fastestmirror
> Setting up Install Process
> Loading mirror speeds from cached hostfile
>  * base: ftp.nara.wide.ad.jp
>  * epel: ftp.iij.ad.jp
>  * extras: ftp.nara.wide.ad.jp
>  * updates: mirror.nus.edu.sg
> Resolving Dependencies
> --> Running transaction check
> ---> Package bash-completion.noarch 1:1.3-7.el6 will be installed
> --> Finished Dependency Resolution
>
> Dependencies Resolved
>
> ================================================================================
>  Package                 Arch           Version              Repository    Size
> ================================================================================
> Installing:
>  bash-completion         noarch         1:1.3-7.el6          epel         216 k
>
> Transaction Summary
> ================================================================================
> Install       1 Package(s)
>
> Total download size: 216 k
> Installed size: 576 k
> Downloading Packages:
> bash-completion-1.3-7.el6.noarch.rpm                     | 216 kB     00:00
> warning: rpmts_HdrFromFdno: Header V3 RSA/SHA256 Signature, key ID 0608b895: NOKEY
> Retrieving key from file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
> Importing GPG key 0x0608B895:
>  Userid : EPEL (6) <epel@fedoraproject.org>
>  Package: epel-release-6-8.noarch (@extras)
>  From   : /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
> Running rpm_check_debug
> Running Transaction Test
> Transaction Test Succeeded
> Running Transaction
>   Installing : 1:bash-completion-1.3-7.el6.noarch                           1/1
>   Verifying  : 1:bash-completion-1.3-7.el6.noarch                           1/1
>
> Installed:
>   bash-completion.noarch 1:1.3-7.el6
>
> Complete!
> ```

新たにシェルを起動すると、completionを利用可能状態です。

```
$ bash
```

### `mussel-completion.bash`の設定

ソースからインストールした場合の手順です。`mussel-completion.bash`は、`/opt/axsh/wakame-vdc/client/mussel/completion/mussel-completion.bash`に配置されています。`.bashrc`を修正し、以下の内容を追加します。

```
source /opt/axsh/wakame-vdc/client/mussel/completion/mussel-completion.bash
```

変更した内容を現在のシェルに反映します。

```
$ source ~/.bashrc
```

### 変更内容の確認

正しく設定されている事を確認する為に`complete`コマンドを実行します。

```
$ complete
```

以下の様に表示されていれば、正しく設定されています。

> ```
> $ complete
> complete -F _mussel mussel
> ```

これにより`mussel`操作効率が向上します。

### completionを試す

試しに、`mussel`の後にTABキーを入力してみて下さい。

```
$ mussel #ここでTABを入力
```

TABキーをすると、第一引数の候補が表示されます。

> ```
> $ mussel
> alarm                instance             network_vif
> backup_object        instance_monitoring  network_vif_monitor
> backup_storage       ip_handle            security_group
> dc_network           ip_pool              ssh_key_pair
> host_node            load_balancer        storage_node
> image                network              volume
> ```

上記例ではコマンド候補が表示されました。入力支援機能はコマンド候補を表示してくるだけでなく、オプション名やオプションの値の入力支援もします。コマンドの使い方が分からなくなった時は、「とりあえずTABキーを入力」。慣れて来ると、マニュアルに頼る事なくWakame-vdcのリソースを操作出来ます。

## `~/.musselrc`の設定

musselは実行時に、設定ファイル`~/.musselrc`を読み込む仕組みを備えています。`~/.musselrc`には、APIを呼び出す為の必須項目を設定します。必須項目は2つです。

1. `DCMGR_HOST`: Wakame-vdc APIのIPアドレス
2. `account_id`: アカウントID

### `~/.musselrc`の作成

```
$ vi ~/.musselrc
```

```
DCMGR_HOST=127.0.0.1
account_id=a-shpoolxx
```

### 環境変数`MUSSEL_RC`によるパス指定

開発環境やテスト環境など、環境毎に`~/.musselrc`を別の場所に設置したい場合があります。そう言った使用条件を満たす為に、musselは環境変数`MUSSEL_RC`によるパス指定機能を備えています。未指定の場合は、`~/.musselrc`が読み込まれます。

1. 環境変数`MUSSEL_RC`指定の場合は、`MUSSEL_RC`を読み込む
2. 環境変数`MUSSEL_RC`未指定の場合は、`~/.musselrc`を読み込む

```
$ vi /path/to/musselrc
```

`MUSSEL_RC`指定するには2つの方法があります。

シェルに反映：

```
$ export MUSSEL_RC=/path/to/musselrc
$ mussel <resource> <command> ...
```

実行時に一時反映：

```
$ MUSSEL_RC=/path/to/musselrc mussel <resource> <command> ...
```

環境や利用状況に合わせ、臨機応変に使い分けられます。

# musselチュートリアル

musselからインスタンスを作成して行きます。

## インスタンス作成へ向けて事前準備

インスタンスを作成するには、事前準備が必要です。

+ SSHキーペアの作成
+ セキュリティグループの作成

事前準備をして行きます。

### `ssh_key_pair`: SSHキーペア操作

Wakame-vdcインスタンスにログインするには、SSHキーペアが必要です。まずはSSHキーペアの作成とキーペアの登録をして行きます。

#### 1: キーペアの作成

`ssh-keygen`コマンドでキーペアを作成します。

```
$ ssh-keygen -N "" -f mykeypair
```

実行結果例：

> ```
> $ ssh-keygen -N "" -f mykeypair
> Generating public/private rsa key pair.
> Your identification has been saved in mykeypair.
> Your public key has been saved in mykeypair.pub.
> The key fingerprint is:
> 37:14:43:28:f7:0f:9e:be:fc:80:63:19:bf:48:b0:f2 centos@wakame-vdc-1box
> The key's randomart image is:
> +--[ RSA 2048]----+
> |         o+      |
> |      . o  o     |
> |       o ..      |
> |         .o      |
> |      . S.o+     |
> |       o *o..    |
> |    . . *.o      |
> |     o o +.o     |
> |      E . +o.    |
> +-----------------+
> ```

`ssh-keygen`コマンド実行後、ファイルが2つ生成されます。

1. `mykeypair`：秘密鍵
2. `mykeypair.pub`：公開鍵

APIに登録するのは、公開鍵です。秘密鍵は、インスタンスにログインする時に使います。

#### 2: 公開鍵の登録

公開鍵`mykeypair.pub`をAPIに登録します。

```
$ mussel ssh_key_pair create --public-key mykeypair.pub --display-name mykeypair
```

実行結果例：

> ```
> $ mussel ssh_key_pair create --public-key mykeypair.pub --display-name mykeypair
> ---
> :id: ssh-ruekc3bs
> :account_id: a-shpoolxx
> :uuid: ssh-ruekc3bs
> :finger_print: 37:14:43:28:f7:0f:9e:be:fc:80:63:19:bf:48:b0:f2
> :public_key: |
>   ssh-rsa > AAAAB3NzaC1yc2EAAAABIwAAAQEAtIALiOUija7BgqCbhSO6xDgHndjV9pI0FBYdg0vbmP2eSAuiHnj7b66+u9x5q71y0B3ja3qg74R+CTKIVAOZ2M2+nIJVFgH2Koalu7HMSj+pfUAWxA5KG2Wg2ic0ndxYdp2W97SVSFOPAuPHJLgZvLymJu3YdNZuLmbku4eVrEb0N7APZMOcf6aBsq9A2ij4ism1K2UekbPCdpSP1bSUx9gPpGOVsNTL9qXkodNXLZX3BDW+2cf08TtfV1DjqbHrSjJP64sziCvxnwyWiHzui97g3MRUul8JhM5lQcgpTcQa8vkffBzEf73UbUFW5+2LtpI52SWxey+BmAjhFsrFHw== centos@wakame-vdc-1box
> :description: ''
> :created_at: 2015-04-08 05:23:12.000000000 Z
> :updated_at: 2015-04-08 05:23:12.000000000 Z
> :service_type: std
> :display_name: mykeypair
> :deleted_at:
> :labels: []
> ```

登録が完了すると、uuidが表示されます。上記の場合では、`ssh-ruekc3bs`がuuidです。

#### 3: 登録済み公開鍵の確認

公開鍵が正常に登録された事を確認します。実行時にuuidを指定します。

```
$ mussel ssh_key_pair show ssh-ruekc3bs
```

実行結果例：

> ```
> $ mussel ssh_key_pair show ssh-ruekc3bs
> ---
> :id: ssh-ruekc3bs
> :account_id: a-shpoolxx
> :uuid: ssh-ruekc3bs
> :finger_print: 37:14:43:28:f7:0f:9e:be:fc:80:63:19:bf:48:b0:f2
> :public_key: |
>   ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAtIALiOUija7BgqCbhSO6xDgHndjV9pI0FBYdg0vbmP2eSAuiHnj7b66+u9x5q71y0B3ja3qg74R+CTKIVAOZ2M2+nIJVFgH2Koalu7HMSj+pfUAWxA5KG2Wg2ic0ndxYdp2W97SVSFOPAuPHJLgZvLymJu3YdNZuLmbku4eVrEb0N7APZMOcf6aBsq9A2ij4ism1K2UekbPCdpSP1bSUx9gPpGOVsNTL9qXkodNXLZX3BDW+2cf08TtfV1DjqbHrSjJP64sziCvxnwyWiHzui97g3MRUul8JhM5lQcgpTcQa8vkffBzEf73UbUFW5+2LtpI52SWxey+BmAjhFsrFHw== centos@wakame-vdc-1box
> :description: ''
> :created_at: 2015-04-08 05:23:12.000000000 Z
> :updated_at: 2015-04-08 05:23:12.000000000 Z
> :service_type: std
> :display_name: mykeypair
> :deleted_at:
> :labels: []
> ```

正常に公開鍵が登録されてる事を確認出来ました。

### `security_group`: セキュリティグループ操作

Wakame-vdcでは、全てのインスタンスにセキュリティグループ（ファイヤーウォール機能）が設定されます。インスタンスへの接続を許可設定しない限り、インスタンスへの接続は全て遮断されます。それでは、インスタンスへ接続する為の接続許可を設定して行きます。

#### 1: 接続許可ルールの定義

APIにルールを登録する為に、ルールファイルを作成します。

```
$ vi sgrule.txt
```

```
icmp:-1,-1,ip4:0.0.0.0/0
tcp:22,22,ip4:0.0.0.0/0
tcp:80,80,ip4:0.0.0.0/0
```

ルールの概要は下記の通りです。

+ `icmp:-1,-1,ip4:0.0.0.0/0`: ICMP許可(ping・疎通確認用)
+ `tcp:22,22,ip4:0.0.0.0/0`: TCP22番許可(SSH用)
+ `tcp:80,80,ip4:0.0.0.0/0`: TCP80番許可(HTTP用)

後述の作業でTCP80も必要になるので、ここでTCP80も許可しておきます。

#### 2: セキュリティグループの登録

作成したルールファイルを基に、セキュリティグループを登録します。

```
$ mussel security_group create --rule sgrule.txt --display-name sgrule
```

実行結果例：

> ```
> $ mussel security_group create --rule sgrule.txt --display-name sgrule
> ---
> :id: sg-nhrd602s
> :account_id: a-shpoolxx
> :uuid: sg-nhrd602s
> :created_at: 2015-04-08 05:27:57.000000000 Z
> :updated_at: 2015-04-08 05:27:57.000000000 Z
> :description:
> :rule: |
>   icmp:-1,-1,ip4:0.0.0.0/0
>   tcp:22,22,ip4:0.0.0.0/0
>   tcp:80,80,ip4:0.0.0.0/0
> :service_type: std
> :display_name: sgrule
> :labels: []
> :rules:
> - :ip_protocol: icmp
>   :icmp_type: -1
>   :icmp_code: -1
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> - :ip_protocol: tcp
>   :ip_fport: 22
>   :ip_tport: 22
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> - :ip_protocol: tcp
>   :ip_fport: 80
>   :ip_tport: 80
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> ```

登録が完了すると、uuidが表示されます。上記の場合では、`sg-nhrd602s`がuuidです。

#### 3: 登録済みセキュリティグループの確認

公開鍵が正常に登録された事を確認します。実行時にuuidを指定します。

```
$ mussel security_group show sg-nhrd602s
```

実行結果例：

> ```
> $ mussel security_group show sg-nhrd602s
> ---
> :id: sg-nhrd602s
> :account_id: a-shpoolxx
> :uuid: sg-nhrd602s
> :created_at: 2015-04-08 05:27:57.000000000 Z
> :updated_at: 2015-04-08 05:27:57.000000000 Z
> :description:
> :rule: |
>   icmp:-1,-1,ip4:0.0.0.0/0
>   tcp:22,22,ip4:0.0.0.0/0
>   tcp:80,80,ip4:0.0.0.0/0
> :service_type: std
> :display_name: sgrule
> :labels: []
> :rules:
> - :ip_protocol: icmp
>   :icmp_type: -1
>   :icmp_code: -1
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> - :ip_protocol: tcp
>   :ip_fport: 22
>   :ip_tport: 22
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> - :ip_protocol: tcp
>   :ip_fport: 80
>   :ip_tport: 80
>   :protocol: ip4
>   :ip_source: 0.0.0.0/0
> ```

正常にセキュリティグループが登録されてる事を確認出来ました。

### `image`: マシンイメージ情報

インスタンスを作成するには、マシンイメージが必要です。本節では、インスタンス作成時に使用するマシンイメージの内容を確認します。vdc1boxにはデモ用途の登録済みマシンイメージがありますので、それをデモ用マシンイメージとして使って行きます。

#### 1: マシンイメージの確認

デモ用マシンイメージのuuidは`wmi-centos1d64`です。

```
$ mussel image show wmi-centos1d64
```

実行結果例：

> ```
> $ mussel image show wmi-centos1d64
> ---
> :id: wmi-centos1d64
> :account_id: a-shpoolxx
> :uuid: wmi-centos1d64
> :created_at: 2015-04-07 12:36:11.000000000 Z
> :updated_at: 2015-04-07 12:36:11.000000000 Z
> :boot_dev_type: 2
> :arch: x86_64
> :description: centos-6.6.x86_64.openvz.md.raw.tar.gz local
> :is_public: true
> :state: available
> :features:
>   :virtio: true
> :file_format: raw
> :root_device: label:root
> :is_cacheable: true
> :instance_model_name: ''
> :parent_image_id:
> :service_type: std
> :display_name: centos1d64
> :backup_object_id: bo-centos1d64
> :deleted_at:
> :volumes: []
> :vifs: []
> :os_type: linux
> :labels: []
> ```

使用予定のデモ用マシンイメージが登録されてる事を確認出来ました。

### `network`: ネットワーク情報

インスタンスに割り当てるネットワーク情報を確認しておきます。

#### 1: ネットワーク情報の確認

デモ用ネットワークのuuidは`nw-demo1`です。

```
$ mussel network show nw-demo1
```

実行結果例：

> ```
> $ mussel network show nw-demo1
> ---
> :id: nw-demo1
> :account_id: a-shpoolxx
> :uuid: nw-demo1
> :ipv4_network: 10.0.2.0
> :ipv4_gw: 10.0.2.2
> :prefix: 24
> :metric: 9
> :domain_name: vdc.local
> :dns_server:
> :dhcp_server:
> :metadata_server:
> :metadata_server_port:
> :nat_network_id:
> :description: ''
> :created_at: 2015-04-07 12:36:10.000000000 Z
> :updated_at: 2015-04-07 12:36:10.000000000 Z
> :network_mode: securitygroup
> :bandwidth:
> :ip_assignment: asc
> :editable: false
> :service_type: std
> :display_name: demo1
> :bandwidth_mark: 1
> :network_services:
> - :name: external-ip
>   :network_id: nw-demo1
>   :network_vif_id: vif-kmpsuaz1
>   :address:
>   :mac_addr: 52:54:00:3a:ad:a4
>   :incoming_port:
>   :outgoing_port:
>   :created_at: 2015-04-07 12:36:11.000000000 Z
>   :updated_at: 2015-04-07 12:36:11.000000000 Z
> :dc_network:
>   :id: dcn-xqapjqtk
>   :name: public
>   :description: ''
>   :created_at: 2015-04-07 12:36:10.000000000 Z
>   :updated_at: 2015-04-07 12:36:10.000000000 Z
>   :uuid: dcn-xqapjqtk
>   :vlan_lease_id:
>   :offering_network_modes:
>   - securitygroup
>   :allow_new_networks: false
> ```

ネットワークのuuidは`nw-demo1`です。

### vifの設計

インスタンスに追加するvifには、更に幾つかの情報が必要です。セキュリティグループはvifに対して設定されます。更に、複数vifを追加出来ます。そうすると、3つの項目を定義する必要があります。

1. Q. vifは、どのネットワークに参加するのか?
2. Q. vifは、どのセキュリティグループが設定するのか？
3. Q. vifの数は？

#### 1: vifの構成を定義

複雑な構成を設計可能ですが、最小の構成を理解しておく必要があります。そこで今回は最小構成を設計します。

1. Q. vifは、どのネットワークに参加するのか?
    + A. `nw-demo1`
2. Q. vifは、どのセキュリティグループを設定するのか?
    + A. `sg-nhrd602s`
3. Q. vifの数は？
    + A. `1`枚 ・ `eth0`

これらをWakame-vdc用設定情報として書き出します。

#### 2: 構成内容をJSONで記述

JSON形式で定義します。

```
$ vi vifs.json
```

```
{
"eth0":{"network":"nw-demo1","security_groups":"sg-nhrd602s"}
}
```

+ `eth0`を追加し
+ 参加するネットワークは、`nw-demo1`
+ 適用するセキュリティグループは、`sg-nhrd602s`

`vifs.json`を作成しました。

### 成果物の整理

ここまでの成果物を整理します。

+ SSHキーペア: `ssh-ruekc3bs`
+ SSH秘密鍵: `mykeypair`
+ セキュリティグループ: `sg-nhrd602s`
+ マシンイメージ: `wmi-centos1d64`
+ ネットワーク：`nw-demo1`
+ vif構成：`vifs.json`

この後のインスタンス操作で使うので、必ず整理しておいてください。

## `instance`: インスタンスの操作

いよいよ、インスタンス操作を行います。事前準備にて作成した各種uuidを整理して下さい。本節では以下のuuidと秘密鍵を使って行きます。

+ SSHキーペア: `ssh-ruekc3bs`
+ SSH秘密鍵: `mykeypair`
+ マシンイメージ: `wmi-centos1d64`
+ vif構成：`vifs.json`

### インスタンスの作成と破棄

インスタンスの作成と破棄を行います。

#### 1: インスタンスの作成

いよいよインスタンス作成です。作成時にはuuidと共に、インスタンスのスペック用パラメタを指定します。

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-centos1d64 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name vdc-instance \
 --vifs vifs.json
```

インスタンスのスペック用パラメタの内容は、以下の通りです。

+ `--hypervisor`: ハイパーバイザーの種類
+ `--cpu-cores`: CPUコア数
+ `--memory-size`: メモリサイズ

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-centos1d64 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name vdc-instance --vifs vifs.json
> ---
> :id: i-31zmj9fr
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:35:07.000000000 Z
> :updated_at: 2015-04-08 05:35:07.524702114 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-iednolgh
>   :state: scheduling
> :vif: []
> :hostname: 31zmj9fr
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-31zmj9fr
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:35:07.000000000 Z
>   :updated_at: 2015-04-08 05:35:07.000000000 Z
> :boot_volume_id: vol-iednolgh
> :encrypted_password:
> ```

上記例では、`i-31zmj9fr`と言うuuidでインスタンスが作成された事が分かります。

なお、インスタンス作成を命令した時点では、インスタンスは作成されず、作成予約を受け付けた状態です。それを示す様に、実行結果では`:state: scheduling`となっています。また、IPアドレスが確定していません。後にスケジューラがIPアドレスを決定します。

#### 2: インスタンスのIPアドレスを確認

インスタンスにログインするには、IPアドレスが必要です。

インスタンス作成命令から一定時間（2～3数秒）経過すると、スケジューラがIPアドレスを決定します。決定後はインスタンスのIPアドレスを確認出来ます。インスタンスの情報を確認・表示します。

```
$ mussel instance show i-31zmj9fr
```

実行結果例：

> ```
> $ mussel instance show i-31zmj9fr
> ---
> :id: i-31zmj9fr
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:35:07.000000000 Z
> :updated_at: 2015-04-08 05:35:28.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-iednolgh
>   :state: attached
> :vif:
> - :vif_id: vif-l9rjt9q2
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 31zmj9fr
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-31zmj9fr
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:35:07.000000000 Z
>   :updated_at: 2015-04-08 05:35:07.000000000 Z
> :boot_volume_id: vol-iednolgh
> :encrypted_password:
> ```

上記例内容では、IPアドレスが`10.0.2.100`です。

もう1つ、注目すべき内容があります。それは、先ほど`scheduling`となっていた`state`です。 上記内容では`:state: running`となっています。これはインスタンスが正常に起動した事を意味しています。サーバ負荷が高い時は、`running`になるまで数十秒～数分かかる事があります。

#### 3:インスタンスにSSH接続

インスタンスにSSH接続、ログインします。事前に作成したSSHキーペアの秘密鍵を使います。ログイン時に指定するユーザー名は`root`です。

+ ユーザー名：`root`
+ IPアドレス：`10.0.2.100`
+ 秘密鍵：`mykeypair`

```
$ ssh root@10.0.2.100 -i mykeypair
```

実行結果例：

> ```
> $ ssh root@10.0.2.100 -i mykeypair
> Warning: Permanently added '10.0.2.100' (RSA) to the list of known hosts.
> [root@31zmj9fr ~]#
> ```

インスタンスからログアウトする場合は`exit`コマンドを実行します。

```
# exit
```

実行結果例：

> ```
> [root@31zmj9fr ~]# exit
> logout
> Connection to 10.0.2.100 closed.
> ```

インスタンスが起動し、ログイン出来る所まで確認出来ました。

#### 4:インスタンスの破棄

作成したインスタンスを破棄します。

```
$ mussel instance destroy i-31zmj9fr
```

実行結果例：

> ```
> $ mussel instance destroy i-31zmj9fr
> ---
> - i-31zmj9fr
> ```

#### ここまでのまとめ

インスタンスの作成からSSHログイン、そして、インスタンスの破棄と言うもっとも基本的なインスタンス操作を体験しました。一連の操作は、全てにおいて基本操作となるので、十分慣れておきましょう。

### インスタンスのpoweroffとpoweron

インスタンスを破棄してしまうと、データは失われますが、poweroff/poweronの場合、データは消えません。必要な時だけCPUやメモリが使われるので、システムリソースを節約出来ます。

#### 事前準備

poweroff/poweronするには、対象となるインスタンスが必要です。細かな操作説明は省略します

##### 1: インスタンスの作成

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-centos1d64 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name vdc-instance \
 --vifs vifs.json
```

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-centos1d64 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name vdc-instance --vifs vifs.json
> ---
> :id: i-3dyfffr2
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:37:48.000000000 Z
> :updated_at: 2015-04-08 05:37:48.240557791 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2zlab2pu
>   :state: scheduling
> :vif: []
> :hostname: 3dyfffr2
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-3dyfffr2
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:37:48.000000000 Z
>   :updated_at: 2015-04-08 05:37:48.000000000 Z
> :boot_volume_id: vol-2zlab2pu
> :encrypted_password:
> ```

インスタンスのuuidは、`i-3dyfffr2`です。

##### 2: インスタンスの状態を確認

```
$ mussel instance show i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance show i-3dyfffr2
> ---
> :id: i-3dyfffr2
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:37:48.000000000 Z
> :updated_at: 2015-04-08 05:38:03.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2zlab2pu
>   :state: attached
> :vif:
> - :vif_id: vif-fnh7qx5g
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 3dyfffr2
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-3dyfffr2
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:37:48.000000000 Z
>   :updated_at: 2015-04-08 05:37:48.000000000 Z
> :boot_volume_id: vol-2zlab2pu
> :encrypted_password:
> ```

`:state:`が`running`になってる事を確認します。poweroffする為の前提条件です。

#### 実作業

##### 1: インスタンスのpoweroff

uuidを指定して実行します。

```
$ mussel instance poweroff i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance poweroff i-3dyfffr2
> ---
> :instance_id: i-3dyfffr2
> ```

##### 2: poweroff後のインスタンスの状態を確認

```
$ mussel instance show i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance show i-3dyfffr2
> ---
> :id: i-3dyfffr2
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:37:48.000000000 Z
> :updated_at: 2015-04-08 05:38:25.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: halted
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2zlab2pu
>   :state: attached
> :vif:
> - :vif_id: vif-fnh7qx5g
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 3dyfffr2
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-3dyfffr2
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:37:48.000000000 Z
>   :updated_at: 2015-04-08 05:37:48.000000000 Z
> :boot_volume_id: vol-2zlab2pu
> :encrypted_password:
> ```

`:state:`が`halted`になってる事を確認します。poweronする為の前提条件です。

##### 3: インスタンスのpoweron

uuidを指定して実行します。

```
$ mussel instance poweron i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance poweron i-3dyfffr2
> ---
> :instance_id: i-3dyfffr2
> ```

成功すると、uuidが表示されます。

##### 4: poweron後のインスタンスの状態を確認

```
$ mussel instance show i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance show i-3dyfffr2
> ---
> :id: i-3dyfffr2
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 05:37:48.000000000 Z
> :updated_at: 2015-04-08 05:38:52.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2zlab2pu
>   :state: attached
> :vif:
> - :vif_id: vif-fnh7qx5g
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 3dyfffr2
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-3dyfffr2
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 05:37:48.000000000 Z
>   :updated_at: 2015-04-08 05:37:48.000000000 Z
> :boot_volume_id: vol-2zlab2pu
> :encrypted_password:
> ```

`:state:`が`running`になってる事を確認します。

#### 後処理

##### 1:インスタンスの破棄

```
$ mussel instance destroy i-3dyfffr2
```

実行結果例：

> ```
> $ mussel instance destroy i-3dyfffr2
> ---
> - i-3dyfffr2
> ```

#### ここまでのまとめ

起動したインスタンスをpoweroff/poweronする操作を行いました。poweroff/poweronは後述のbackupで使いますので、十分慣れておきましょう。

### インスタンスのbackupとマシンイメージ登録、そしてインスタンスの作成

本節は、前節のpoweroff/poweronの応用編です。backupを連携させる事により、ユーザーが任意のデータを登録したマシンイメージを新規作成出来ます。前節までの様に、毎回最小構成のマシンイメージから環境構築しても良いですが、構築作業手間や作業ブレ等・作業漏れを考慮すると、必ずしも良い事とは言えません。解決策の1つが、事前にパッケージをインストールしたマシンイメージを作成する事です。それにより、構築済みの環境が簡単に手に入ります。

#### 事前準備

backup対象となるインスタンスが必要です。細かな操作説明は省略します。

##### 1: インスタンスの作成

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-centos1d64 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name vdc-instance \
 --vifs vifs.json
```

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-centos1d64 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name vdc-instance --vifs vifs.json
> ---
> :id: i-0yuzzyd7
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 07:02:13.000000000 Z
> :updated_at: 2015-04-08 07:02:13.708428544 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-smguw2al
>   :state: scheduling
> :vif: []
> :hostname: 0yuzzyd7
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-0yuzzyd7
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 07:02:13.000000000 Z
>   :updated_at: 2015-04-08 07:02:13.000000000 Z
> :boot_volume_id: vol-smguw2al
> :encrypted_password:
> ```

インスタンスのuuidは、`i-0yuzzyd7`です。

##### 2: インスタンスの状態を確認

```
$ mussel instance show i-0yuzzyd7
```

実行結果例：

>```
> $ mussel instance show i-0yuzzyd7
> ---
> :id: i-0yuzzyd7
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 07:02:13.000000000 Z
> :updated_at: 2015-04-08 07:02:26.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-smguw2al
>   :state: attached
> :vif:
> - :vif_id: vif-cq6npth8
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 0yuzzyd7
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-0yuzzyd7
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 07:02:13.000000000 Z
>   :updated_at: 2015-04-08 07:02:13.000000000 Z
> :boot_volume_id: vol-smguw2al
> :encrypted_password:
> ```

`:state:`が`running`になってる事を確認します。

##### 3: インスタンスのpoweroff

```
$ mussel instance poweroff i-0yuzzyd7
```

実行結果例：

> ```
> $ mussel instance poweroff i-0yuzzyd7
> ---
> :instance_id: i-0yuzzyd7
> ```

poweroffまで完了しました。

##### 4: インスタンスの状態を確認

インスタンスの状態を確認します。

```
$ mussel instance show i-0yuzzyd7
```

> ```
> $ mussel instance show i-0yuzzyd7
> ---
> :id: i-0yuzzyd7
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-centos1d64
> :created_at: 2015-04-08 07:02:13.000000000 Z
> :updated_at: 2015-04-08 07:04:37.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: halted
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-smguw2al
>   :state: attached
> :vif:
> - :vif_id: vif-cq6npth8
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: 0yuzzyd7
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-0yuzzyd7
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 07:02:13.000000000 Z
>   :updated_at: 2015-04-08 07:02:13.000000000 Z
> :boot_volume_id: vol-smguw2al
> :encrypted_password:
> ```

`:state:`が`halted`になっている事を確認します。これは次のbackupを実行する為の前提条件です。

#### 実作業

##### 1: インスタンスのbackup(新マシンイメージの作成)

uuidを指定して実行します。

```
$ mussel instance backup i-0yuzzyd7
```

実行結果例：

> ```
> $ mussel instance backup i-0yuzzyd7
> ---
> :instance_id: i-0yuzzyd7
> :image_id: wmi-4yalh576
> :backup_object_ids:
> - bo-3bm4h97t
> ```

新マシンイメージのuuidは`wmi-4yalh576`です。

##### 2: 新マシンイメージの内容・進捗を確認

uuidを指定して実行します。

```
$ mussel image show wmi-4yalh576
```

実行結果例：

> ```
> $ mussel image show wmi-4yalh576
> ---
> :id: wmi-4yalh576
> :account_id: a-shpoolxx
> :uuid: wmi-4yalh576
> :created_at: 2015-04-08 07:06:20.000000000 Z
> :updated_at: 2015-04-08 07:06:20.000000000 Z
> :boot_dev_type: 2
> :arch: x86_64
> :description: centos-6.6.x86_64.openvz.md.raw.tar.gz local
> :is_public: false
> :state: creating
> :features:
>   :virtio: true
> :file_format: raw
> :root_device: label:root
> :is_cacheable: false
> :instance_model_name: ''
> :parent_image_id: wmi-centos1d64
> :service_type: std
> :display_name: centos1d64
> :backup_object_id: bo-3bm4h97t
> :deleted_at:
> :volumes: []
> :vifs: []
> :os_type: linux
> :labels: []
> ```

上記では`:state:`が`creating`となっています。

インスタンスのバックアップには、2・3分の時間が必要です。小さいマシンイメージでも、サイズは数百メガあるので、数分の待ち時間が必要です。新マシンイメージからインスタンスを作成するには、バックアップ完了が前提条件です。`:state:`が`available`になるまで、状態を確認します。

```
$ mussel image show wmi-4yalh576
```

実行結果例：

> ```
> $ mussel image show wmi-4yalh576
> ---
> :id: wmi-4yalh576
> :account_id: a-shpoolxx
> :uuid: wmi-4yalh576
> :created_at: 2015-04-08 07:06:20.000000000 Z
> :updated_at: 2015-04-08 07:07:06.000000000 Z
> :boot_dev_type: 2
> :arch: x86_64
> :description: centos-6.6.x86_64.openvz.md.raw.tar.gz local
> :is_public: false
> :state: available
> :features:
>   :virtio: true
> :file_format: raw
> :root_device: label:root
> :is_cacheable: false
> :instance_model_name: ''
> :parent_image_id: wmi-centos1d64
> :service_type: std
> :display_name: centos1d64
> :backup_object_id: bo-3bm4h97t
> :deleted_at:
> :volumes: []
> :vifs: []
> :os_type: linux
> :labels: []
> ```

`:state:`が`available`になりました。

##### 3: 新マシンイメージからインスタンスの作成

新マシンイメージ`wmi-4yalh576`を指定してインスタンスを作成します。

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-4yalh576 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name vdc-instance \
 --vifs vifs.json
```

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-4yalh576 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name vdc-instance --vifs vifs.json
> ---
> :id: i-hbkkrmkb
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-4yalh576
> :created_at: 2015-04-08 07:08:33.000000000 Z
> :updated_at: 2015-04-08 07:08:33.137012368 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-ja0kj1f0
>   :state: scheduling
> :vif: []
> :hostname: hbkkrmkb
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-hbkkrmkb
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 07:08:33.000000000 Z
>   :updated_at: 2015-04-08 07:08:33.000000000 Z
> :boot_volume_id: vol-ja0kj1f0
> :encrypted_password:
> ```

uuidは`i-hbkkrmkb`です。これまでのデモマシンイメージとは違い、`:image_id:`が`wmi-4yalh576`です。

##### 4: 新インスタンスのIPアドレスを確認

```
$ mussel instance show i-hbkkrmkb
```

実行結果例：

> ```
> $ mussel instance show i-hbkkrmkb
> ---
> :id: i-hbkkrmkb
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-4yalh576
> :created_at: 2015-04-08 07:08:33.000000000 Z
> :updated_at: 2015-04-08 07:08:51.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-ja0kj1f0
>   :state: attached
> :vif:
> - :vif_id: vif-hpkibqet
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: hbkkrmkb
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: vdc-instance
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-hbkkrmkb
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 07:08:33.000000000 Z
>   :updated_at: 2015-04-08 07:08:33.000000000 Z
> :boot_volume_id: vol-ja0kj1f0
> :encrypted_password:
> ```

インスタンスのuuidは、`10.0.2.100`です。

##### 5: 必要であればログイン確認

正常にマシンイメージが作成されているかどうかは、ログインする事により確認出来ます。

+ ユーザー名：`root`
+ IPアドレス：`10.0.2.100`
+ 秘密鍵：`mykeypair`

```
$ ssh root@10.0.2.100 -i mykeypair
```

#### 後処理

##### 1: インスタンスの破棄

```
$ mussel instance destroy i-0yuzzyd7
```

実行結果例：

> ```
> $ mussel instance destroy i-0yuzzyd7
> ---
> - i-0yuzzyd7
> ```

##### 2: 新インスタンスの破棄

```
$ mussel instance destroy i-hbkkrmkb
```

実行結果例：

> ```
> $ mussel instance destroy i-hbkkrmkb
> ---
> - i-hbkkrmkb
> ```

## `load_balancer`: ロードバランサーの操作

ロードバランサーの操作を行っていきます。

### ロードバランサーの作成と破棄

ロードバランサーの作成と破棄は、インスタンスの基本操作と同等です。

#### 1: ロードバランサーの作成

ロードバランサー作成には特有のパラメタを指定します。

```
$ mussel load_balancer create \
 --balance-algorithm leastconn \
 --engine haproxy \
 --instance-port 80 \
 --instance-protocol http \
 --max-connection 1000 \
 --port 80 \
 --protocol http \
 --display-name lb80
```

パラメタの内容は、以下の通りです。一般的なHTTP用のロードバランサーを作成します。

+ `--balance-algorithm`: 負荷分散アルゴリズム
+ `--engine`: ロードバランサー用エンジン種類
+ `--instance-port`: バランス対象インスタンスのポート番号
+ `--instance-protocol`: バランス対象インスタンスのプロトコル
+ `--max-connection`: 最大接続数
+ `--port`: ロードバランサーのポート番号
+ `--protocol`: ロードバランサーのプロトコル

実行結果例：

> ```
> $ mussel load_balancer create --balance-algorithm leastconn --engine haproxy --instance-port 80 --instance-protocol http --max-connection 1000 --port 80 --protocol http --display-name lb80
> ---
> :id: lb-z3261dc9
> :uuid: lb-z3261dc9
> :account_id: a-shpoolxx
> :instance_id: i-mslqyv0i
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 07:12:31.000000000 Z
> :updated_at: 2015-04-08 07:12:31.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: scheduling
> :status: init
> :target_vifs: []
> :vif: []
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

ロードバランサーのuuidは`lb-z3261dc9`です。

#### 2: ロードバランサーのIPアドレスを確認

インスタンスと同様に、作成命令は作成予約で、スケジューラがIPアドレスを決定します。

```
$ mussel load_balancer show lb-z3261dc9
```

実行結果例：

> ```
> $ mussel load_balancer show lb-z3261dc9
> ---
> :id: lb-z3261dc9
> :uuid: lb-z3261dc9
> :account_id: a-shpoolxx
> :instance_id: i-mslqyv0i
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 07:12:31.000000000 Z
> :updated_at: 2015-04-08 07:12:31.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs: []
> :vif:
> - :vif_id: vif-yu39lp8o
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
> - :vif_id: vif-h9lzads6
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

ロードバランサーのIPアドレスは、`10.0.2.100`です。

##### 3: ロードバランサーへのHTTPリクエスト確認

この時点で、ロードバランサーは簡単なHTTPサーバとして機能しています。試しにHTTPリクエストしてみます。

```
$ curl http://10.0.2.100/
```

実行結果例：

> ```
> $ curl http://10.0.2.100/
> <html><body><h1>503 Service Unavailable</h1>
> No server is available to handle this request.
> </body></html>
> ```

HTTPサーバとして動作してる事が分かります。なお、この時点ではバランス対象インスタンスが存在しないので、HTTPステータス503が返って来るのは正しい動きです。

#### 4: ロードバランサーの破棄

作成したロードバランサーを破棄します。

```
$ mussel load_balancer destroy lb-z3261dc9
```

実行結果例：

> ```
> $ mussel load_balancer destroy lb-z3261dc9
> ---
> - lb-z3261dc9
> ```

#### ここまでのまとめ

ロードバランサーの作成と簡単なHTTPリクエスト、そして、ロードバランサーの破棄を行いました。ここまでの操作は、インスタンス作成・破棄と大差がありません。この後は、ロードバランサーとインスタンスを組み合わせて行きます。

### ロードバランサーとwebインスタンスによる複数台構成

複数台構成を構築します。構成するイメージは以下の通りです。

```
client -> lb -+- webA
              +- webB
```

+ ロードバランサー: 1台
+ webインスタンス: 2台

クライアントからのHTTPリクエストを1台のロードバランサーが中継し、2台のwebインスタンスへ振り分けます。

#### 事前準備

##### 1: webインスタンス用マシンイメージの確認

webインスタンスのマシンイメージには、`wmi-lbnode1d64`を使用します。これはロードバランサー連携確認用に特化したマシンイメージです。

```
$ mussel image show wmi-lbnode1d64
```

実行結果例：

> ```
> $ mussel image show wmi-lbnode1d64
> ---
> :id: wmi-lbnode1d64
> :account_id: a-shpoolxx
> :uuid: wmi-lbnode1d64
> :created_at: 2015-04-07 12:36:11.000000000 Z
> :updated_at: 2015-04-07 12:36:11.000000000 Z
> :boot_dev_type: 2
> :arch: x86_64
> :description: lbnode.x86_64.openvz.md.raw.tar.gz local
> :is_public: true
> :state: available
> :features:
>   :virtio: true
> :file_format: raw
> :root_device: label:root
> :is_cacheable: true
> :instance_model_name: ''
> :parent_image_id:
> :service_type: std
> :display_name: lbnode1d64
> :backup_object_id: bo-lbnode1d64
> :deleted_at:
> :volumes: []
> :vifs: []
> :os_type: linux
> :labels: []
> ```

##### 2: webインスタンスAの準備

細かな操作説明は省略します。

###### 1: webインスタンスAの作成

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-lbnode1d64 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name web-a \
 --vifs vifs.json
```

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-lbnode1d64 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name web-a --vifs vifs.json
> ---
> :id: i-zticoe9h
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-lbnode1d64
> :created_at: 2015-04-08 09:50:46.000000000 Z
> :updated_at: 2015-04-08 09:50:46.140596500 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2aqmitnv
>   :state: scheduling
> :vif: []
> :hostname: zticoe9h
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: web-a
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-zticoe9h
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 09:50:46.000000000 Z
>   :updated_at: 2015-04-08 09:50:46.000000000 Z
> :boot_volume_id: vol-2aqmitnv
> :encrypted_password:
> ```

インスタンスのuuidは`i-zticoe9h`です。

###### 2: webインスタンスAのIPアドレスとvifのuuidを確認

```
$ mussel instance show i-zticoe9h
```

実行結果例：

> ```
> $ mussel instance show i-zticoe9h
> ---
> :id: i-zticoe9h
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-lbnode1d64
> :created_at: 2015-04-08 09:50:46.000000000 Z
> :updated_at: 2015-04-08 09:51:06.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-2aqmitnv
>   :state: attached
> :vif:
> - :vif_id: vif-ccrwjrmv
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.100
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: zticoe9h
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: web-a
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-zticoe9h
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 09:50:46.000000000 Z
>   :updated_at: 2015-04-08 09:50:46.000000000 Z
> :boot_volume_id: vol-2aqmitnv
> :encrypted_password:
> ```

インスタンスのIPアドレスは、`10.0.2.100`です。
vifのuuidは`vif-ccrwjrmv`です。

###### 3: webインスタンスAへのHTTPリクエスト確認

`wmi-lbnode1d64`で作ったインスタンスの`/`にHTTPリクエストすると、uuidが返って来ます。

```
$ curl http://10.0.2.100/
```

実行結果例：

> ```
> $ curl http://10.0.2.100/
> i-zticoe9h
> ```

###### 4: webインスタンスAのvifを確認

```
$ mussel network_vif show vif-ccrwjrmv
```

実行結果例：

> ```
> $ mussel network_vif show vif-ccrwjrmv
> ---
> :id: vif-ccrwjrmv
> :uuid: vif-ccrwjrmv
> :ipv4_address: 10.0.2.100
> :nat_ipv4_address:
> :network_id: nw-demo1
> :instance_id: i-zticoe9h
> :security_groups:
> - sg-nhrd602s
> :mac_addr: 52:54:00:a0:55:40
> :network_monitors: []
> :ip_leases:
> - :ipv4: 10.0.2.100
>   :network_id: nw-demo1
>   :ip_handle:
> ```

##### 3: webインスタンスBの準備

細かな操作説明は省略します。

###### 1: webインスタンスBの作成

```
$ mussel instance create \
 --hypervisor openvz \
 --cpu-cores 1 \
 --image-id wmi-lbnode1d64 \
 --memory-size 256 \
 --ssh-key-id ssh-ruekc3bs \
 --display-name web-b \
 --vifs=vifs.json
```

実行結果例：

> ```
> $ mussel instance create --hypervisor openvz --cpu-cores 1 --image-id wmi-lbnode1d64 --memory-size 256 --ssh-key-id ssh-ruekc3bs --display-name web-b --vifs vifs.json
> ---
> :id: i-c07ips1b
> :account_id: a-shpoolxx
> :host_node:
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-lbnode1d64
> :created_at: 2015-04-08 09:53:38.000000000 Z
> :updated_at: 2015-04-08 09:53:38.626235298 Z
> :terminated_at:
> :deleted_at:
> :state: scheduling
> :status: init
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-kacvvihj
>   :state: scheduling
> :vif: []
> :hostname: c07ips1b
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: web-b
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-c07ips1b
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 09:53:38.000000000 Z
>   :updated_at: 2015-04-08 09:53:38.000000000 Z
> :boot_volume_id: vol-kacvvihj
> :encrypted_password:
> ```

インスタンスのuuidは`i-c07ips1b`です。

###### 2: webインスタンスBのIPアドレスとvifのuuidを確認

```
$ mussel instance show i-c07ips1b
```

実行結果例：

> ```
> $ mussel instance show i-c07ips1b
> ---
> :id: i-c07ips1b
> :account_id: a-shpoolxx
> :host_node: hn-1box64
> :cpu_cores: 1
> :memory_size: 256
> :arch: x86_64
> :image_id: wmi-lbnode1d64
> :created_at: 2015-04-08 09:53:38.000000000 Z
> :updated_at: 2015-04-08 09:54:01.000000000 Z
> :terminated_at:
> :deleted_at:
> :state: running
> :status: online
> :ssh_key_pair:
>   :uuid: ssh-ruekc3bs
>   :display_name: mykeypair
> :volume:
> - :vol_id: vol-kacvvihj
>   :state: attached
> :vif:
> - :vif_id: vif-q1uzm9za
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.101
>     :nat_address:
>   :security_groups:
>   - sg-nhrd602s
> :hostname: c07ips1b
> :ha_enabled: 0
> :hypervisor: openvz
> :display_name: web-b
> :service_type: std
> :monitoring:
>   :enabled: false
>   :mail_address: []
>   :items: {}
> :labels:
> - :resource_uuid: i-c07ips1b
>   :name: monitoring.enabled
>   :value_type: 1
>   :value: 'false'
>   :created_at: 2015-04-08 09:53:38.000000000 Z
>   :updated_at: 2015-04-08 09:53:38.000000000 Z
> :boot_volume_id: vol-kacvvihj
> :encrypted_password:
> ```

インスタンスのIPアドレスは、`10.0.2.101`です。
vifのuuidは`vif-q1uzm9za`です。

###### 3: webインスタンスBへのHTTPリクエスト確認

`wmi-lbnode1d64`で作ったインスタンスにHTTPリクエストすると、uuidが返って来ます。

```
$ curl http://10.0.2.101/
```

実行結果例：

> ```
> $ curl http://10.0.2.101/
> i-c07ips1b
> ```

###### 4: webインスタンスBのvifを確認

```
$ mussel network_vif show vif-q1uzm9za
```

実行結果例：

> ```
> $ mussel network_vif show vif-q1uzm9za
> ---
> :id: vif-q1uzm9za
> :uuid: vif-q1uzm9za
> :ipv4_address: 10.0.2.101
> :nat_ipv4_address:
> :network_id: nw-demo1
> :instance_id: i-c07ips1b
> :security_groups:
> - sg-nhrd602s
> :mac_addr: 52:54:00:be:f2:f9
> :network_monitors: []
> :ip_leases:
> - :ipv4: 10.0.2.101
>   :network_id: nw-demo1
>   :ip_handle:
> ```

##### 4: ロードバランサーの準備

細かな操作説明は省略します。

###### 1: ロードバランサーの作成

```
$ mussel load_balancer create \
 --balance-algorithm leastconn \
 --max-connection 1000 \
 --port 80 \
 --protocol http \
 --instance-port 80 \
 --instance-protocol http
```

実行結果例：

> ```
> $ mussel load_balancer create --balance-algorithm leastconn --engine haproxy --instance-port 80 --instance-protocol http --max-connection 1000 --port 80 --protocol http
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: scheduling
> :status: init
> :target_vifs: []
> :vif: []
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 2: ロードバランサーのIPアドレスを確認

```
$ mussel load_balancer show lb-wk919s67
```

実行結果例：

> ```
> $ mussel load_balancer show lb-wk919s67
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs: []
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 3: ロードバランサーへのHTTPリクエスト確認

```
$ curl http://10.0.2.102/
```

実行結果例：

> ```
> $ curl http://10.0.2.102/
> <html><body><h1>503 Service Unavailable</h1>
> No server is available to handle this request.
> </body></html>
> ```

##### 成果物の整理

事前準備したリソースの整理をします。

+ webインスタンスA
    + `i-zticoe9h`
    + `10.0.2.100`
    + `vif-ccrwjrmv`
+ webインスタンスB
    + `i-c07ips1b`
    + `10.0.2.101`
    + `vif-q1uzm9za`
+ lb
    + `lb-wk919s67`
    + `10.0.2.102`

この後の実作業でuuidを中心に進めます。

#### 実作業

事前作業で作成したwebインスタンスをロードバランサに登録し、削除して行きます。

##### 1: ロードバランサーにwebインスタンスのvifを登録

###### 1: ロードバランサーにwebインスタンスAを登録

```
$ mussel load_balancer register lb-474byn9f --vifs vif-ccrwjrmv
```

実行結果例：

> ```
> $ mussel load_balancer register lb-wk919s67 --vifs vif-ccrwjrmv
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs: []
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 2: ロードバランサーにwebインスタンスBを登録

```
$ mussel load_balancer register lb-474byn9f --vifs vif-q1uzm9za
```

実行結果例：

> ```
> $ mussel load_balancer register lb-wk919s67 --vifs vif-q1uzm9za
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs:
> - :network_vif_id: vif-ccrwjrmv
>   :instance_id: i-zticoe9h
>   :display_name: ''
>   :fallback_mode: 'off'
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 3: ロードバランサーの状態を確認

```
$ mussel load_balancer show lb-wk919s67
```

実行結果例：

> ```
> $ mussel load_balancer show lb-wk919s67
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs:
> - :network_vif_id: vif-ccrwjrmv
>   :instance_id: i-zticoe9h
>   :display_name: ''
>   :fallback_mode: 'off'
> - :network_vif_id: vif-q1uzm9za
>   :instance_id: i-c07ips1b
>   :display_name: ''
>   :fallback_mode: 'off'
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

webインスタンスAのvifとwebインスタンスBのvifが、`:target_vifs:`に追加されている事が分かります。

###### 4: ロードバランサー経由のHTTPリクエストを確認

ロードバランサーにHTTPリクエストすると、webインスタンスAとwebインスタンスBに振り分けられるはずです。

```
$ curl http://10.0.2.102/
```

実行結果例：

> ```
> $ curl http://10.0.2.102/
> i-c07ips1b
> ```

> ```
> $ curl http://10.0.2.102/
> i-zticoe9h
> ```

> ```
> $ curl http://10.0.2.102/
> i-c07ips1b
> ```

> ```
> $ curl http://10.0.2.102/
> i-zticoe9h
> ```

webインスタンスAとwebインスタンスBのuuidが表示されました。

##### 2: ロードバランサーからvif(インスタンス)を削除

###### 1: ロードバランサーからwebインスタンスAを削除

```
$ mussel load_balancer unregister lb-wk919s67 --vifs vif-ccrwjrmv
```

実行結果例：

> ```
> $ mussel load_balancer unregister lb-wk919s67 --vifs vif-ccrwjrmv
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs:
> - :network_vif_id: vif-ccrwjrmv
>   :instance_id: i-zticoe9h
>   :display_name: ''
>   :fallback_mode: 'off'
> - :network_vif_id: vif-q1uzm9za
>   :instance_id: i-c07ips1b
>   :display_name: ''
>   :fallback_mode: 'off'
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 2: ロードバランサーからwebインスタンスBを削除

```
$ mussel load_balancer unregister lb-wk919s67 --vifs vif-q1uzm9za
```

実行結果例：

> ```
> $ mussel load_balancer unregister lb-wk919s67 --vifs vif-q1uzm9za
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs:
> - :network_vif_id: vif-q1uzm9za
>   :instance_id: i-c07ips1b
>   :display_name: ''
>   :fallback_mode: 'off'
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

###### 3: ロードバランサーの状態を確認

```
$ mussel load_balancer show lb-wk919s67
```

実行結果例：

> ```
> $ mussel load_balancer show lb-wk919s67
> ---
> :id: lb-wk919s67
> :uuid: lb-wk919s67
> :account_id: a-shpoolxx
> :instance_id: i-rxjmir5r
> :instance_protocol: http
> :instance_port: 80
> :balance_algorithm: leastconn
> :cookie_name:
> :description: ''
> :private_key: ''
> :public_key: ''
> :created_at: 2015-04-08 09:57:00.000000000 Z
> :updated_at: 2015-04-08 09:57:00.000000000 Z
> :deleted_at:
> :display_name: lb80
> :allow_list:
> - 0.0.0.0
> :httpchk_path: ''
> :state: running
> :status: online
> :target_vifs: []
> :vif:
> - :vif_id: vif-35i0pxm6
>   :vif_index: 0
>   :network_id: nw-demo1
>   :ipv4:
>     :address: 10.0.2.102
>     :nat_address:
> - :vif_id: vif-dfgm6s4e
>   :vif_index: 1
>   :network_id: nw-demo8
>   :ipv4:
>     :address: 10.1.0.10
>     :nat_address:
> :inbounds:
> - :port: 80
>   :protocol: http
> ```

webインスタンスAとwebインスタンスBのvifが、`:target_vifs:`から削除されている事が分かります。

###### 4: ロードバランサー経由のHTTPリクエストを確認

```
$ curl http://10.0.2.102/
```

実行結果例：

> ```
> $ curl http://10.0.2.102/
> <html><body><h1>503 Service Unavailable</h1>
> No server is available to handle this request.
> </body></html>
> ```

#### 後処理

##### 1: webインスタンスAの削除

```
$ mussel destroy destroy i-zticoe9h
```

実行結果例：

> ```
> $ mussel instance destroy i-zticoe9h
> ---
> - i-zticoe9h
> ```

##### 2: webインスタンスBの削除

```
$ mussel destroy destroy i-c07ips1b
```

実行結果例：

> ```
> $ mussel instance destroy i-c07ips1b
> ---
> - i-c07ips1b
> ```

##### 3: ロードバランサーの削除

```
$ mussel load_balancer destroy lb-wk919s67
```

実行結果例：

> ```
> $ mussel load_balancer destroy lb-wk919s67
> ---
> - lb-wk919s67
> ```

#### ここまでのまとめ

ロードバランサー1台とwebインスタンス2台構成に触れました。2台構成でしたが、3台以上の構成も可能です。また、HTTPの負荷分散でしたが、他のプロトコルによる構成も可能です。構築したいシステムによって自在に構築可能なので、是非挑戦してみて下さい。
