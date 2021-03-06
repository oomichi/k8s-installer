# Kubernetes offline installer files generator

## 概要

Kubernetes クラスタをオフラインインストールするために必要なファイルをダウンロード
するスクリプトです。

オフラインインストールの手順は以下のようになります。

1. Internet接続されたマシン上で本スクリプトを使用してインストールに必要なファイルを取得します。
具体的には、Dockerやkubeadm/kubeletなどの RPMファイル、コンテナイメージファイルなどです。
2. これらのファイルをまとめた `k8s-offline-files.tar.gz` ファイルが生成されるので、これを何らかの手段(USBメモリ・ハードディスク、VPNなど)
を使用してインストール先のマシンに転送します。
3. インストーラを使用してインストールを実行します。 

## 必要環境

* Internet 接続されているマシン。Kubernetesクラスタを構成するマシンと同一の OS がインストールされていること
    * RHEL 7
    * CentOS 7

## 事前準備

RHEL 7 を使用する場合は、以下手順で rhel-7-server-extras-rpms リポジトリを有効にしておく必要があります。

    $ subscription-manager repos --enable=rhel-7-server-extras-rpms

## Proxy 設定

Internet 接続に Proxy サーバを経由する必要がある場合は、事前に Proxy 設定が必要です。

なお、`config.sh` に Proxy 設定を追記し `sudo ./setup-proxy.sh` を実行することで以下設定を自動投入可能です。

### yum

/etc/yum.conf に `proxy=http://proxy.example.com` の行を追加して Proxy サーバを指定してください。

### Docker

`/etc/systemd/system/docker.service.d/http-proxy.conf` を以下例を参考に作成し、docker を再起動してください。

    [Service]
    Environment="HTTP_PROXY=http://proxy.example.com:8080" "HTTPS_PROXY=http://proxy.example.com:8080" "NO_PROXY=localhost,127.0.0.1,..."

## オフラインインストールファイルの生成

sudo 可能な一般ユーザ権限でログインしてください。

以下手順でオフラインインストールファイルを生成してください。

    $ sudo ./generate-offline.sh

オフラインインストールファイルは `k8s-offline-files.tar.gz` というファイル名で生成されます。

## 注意事項

* 本スクリプトを実行するホストには Docker がインストールされます。
