---
layout: post
title:  "VirtualBox(CentOS7)でポートフォワーディング利用してsshする場合"
categories: VirtualBox CentOS
---

# 前提
- CentOS7.x
- VirtualBox 6.11.22

# やること
## VirtualBoxでのセットアップ時
- iso読み込ませる前に設定→ネットワークを選択し、アダプター1を確認
  - 割り当てにて「NAT」を指定(デフォルトだとこれのはず)
  - 「NAT」だと、ゲストOS→ホストOS→インターネットの経路のみOK
- OSセットアップ時に、ネットワークアダプタを有効化するか聞かれるのでONにする。
- ポートフォワーディング設定も実施しておく。
  - 設定→ネットワーク→アダプター1の「高度」を押下して出て来る「ポートフォワーディング」ボタン押下
  - 右側の+ボタン押下し、以下を入力し、OKを入力
    - ホストポート:7777 (←好きなものでOK。仮)
    - ゲストポート:22 (ssh用なので22固定)
  - 設定モーダルでもOKを入力（ここでOKを押し切らないと設定反映されないので注意）
- VirtualBox起動してiso読み込ませてセッティング

## セットアップ完了後、再起動後もネットワークアダプタが起動するよう設定する
- 現状確認:現状のNICを確認。恐らく「enp3s0」みたいな名前のNICがあるはず。
  ```
  # nmcli device
  ```
- OS再起動後、NICが自動的に使えるようにする
  ```
  # nmcli connection up [表示されたNIC名。eth3s0等]
  # nmcli connection modify [表示されたNIC名。eth3s0等] connection.autoconnect yes
  # systemctl restart NetworkManager
  ```
- NIC起動状況確認
```
# nmcli device
```

### おまけ
- 以下ファイルの手動編集でもOK。CentOS6までは以下ファイルを手動設定していたらしい。nmcliにて以下ファイルへの設定をコマンド経由で実施できるようになったイメージと思われる。
```
/etc/sysconfig/network-scripts/ifcfg-[NIC名]
```

## 再起動する。
- VirtualBoxの中へssh接続する。
  - ホスト名:localhost 7777
  - ポート:22
- 上記ができた場合は色々と設定成功🙆‍♀️
- 上記ができていない場合、CentOSにてNICが起動していないと考えられるので、CentOSの中で再度ターミナル起動して以下を確認。
  ```
  # nmcli device
  ```

# 参考
- [https://rock8beat.hatenablog.com/entry/2019/10/27/002058](https://rock8beat.hatenablog.com/entry/2019/10/27/002058)
- [https://www.unix-power.net/networking/post-676](https://www.unix-power.net/networking/post-676)
- [https://go-journey.club/archives/4124#NIC%E3%81%AE%E8%87%AA%E5%8B%95%E8%B5%B7%E5%8B%95%E3%81%AE%E8%A8%AD%E5%AE%9A](https://go-journey.club/archives/4124#NIC%E3%81%AE%E8%87%AA%E5%8B%95%E8%B5%B7%E5%8B%95%E3%81%AE%E8%A8%AD%E5%AE%9A)
