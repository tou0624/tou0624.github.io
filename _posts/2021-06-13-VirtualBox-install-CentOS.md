---
layout: post
title:  "VirtualBoxへCentOSを入れる"
date:   2021-06-13 00:00:00 +0900
categories: VirtualBox CentOS
---

VirtualBoxにCentOS7を入れることがあったので、その際の調べ物のメモ書き。  

# 前提：記載時のバージョンなど
- CentOS 7.6を利用。CentOS7はまだEOLではない
- VirtualBox 6.11.22
  - 諸事情でVagrantは使わなかったが、Vagrantを利用した起動をするほうがきっと楽なはず。

# 1_インストール
## VirtualBox
[https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)からインストールする。  
未検証だが、Windows10にてDockerとVirtualBoxが共存できるようになったらしいので、今後使いやすくなっているかも🎉

## CentOS
### 手順
1. CentOSの公式サイト([https://www.centos.org/download/](https://www.centos.org/download/))へ遷移。
2. タブ「7(2009)」を押すと表が出てくるので、「x86_64」を押下
![説明画像](https://tou0624.github.io/assets/VirtualBox-install-CentOS/image2.png)
3. ページがいくつか出てくるので、好きなページを押下。こだわりがなければ一番上のURLで大丈夫
4. isoページが出てくるので、何か理由がなければいちばん上の「CentOS7-x86_64-DVD-[番号].iso」をダウンロード。MinimalにするとCUIだけ(黒い画面)になる。デスクトップのインストーラーを利用したセットアップが必要なミドルウェアを入れる場合はこれを使っちゃうと×
![説明画像](https://tou0624.github.io/assets/VirtualBox-install-CentOS/image3.png)

### メモ
#### メジャーバージョンかつ最新ではないマイナーバージョンのisoはどこにある？
[http://vault.centos.org/](http://vault.centos.org/)から探す。

CentOS自体のダウンロードサイトには、各メジャーバージョンの最新マイナーバージョンのisoのみ用意されている。  
例えば現在の最新はCentOS7.9だが、CentOS7.6のisoは置いてない。上記ダウンロード可能なサイトの中をあさってみると、[http://ftp-srv2.kddilabs.jp/Linux/packages/CentOS/7.6.1810/](http://ftp-srv2.kddilabs.jp/Linux/packages/CentOS/7.6.1810/)などへ辿り着く。ここには悲しいことにreadmeしかなくて、[http://vault.centos.org/](http://vault.centos.org/)に行ってねと書いてある
  > If you know what you are doing, and absolutely want to remain at the 7.6.1810
  level, go to http://vault.centos.org/ for packages.

vault...の方にはisoが置いてあるので、isoがありそうな場所を掘り当てる必要がある。ちょっとめんどくさい。

#### isoとは？
光ディスクのデータ💿OSがisoファイルの中に入っているので、読み込ませる

# 2_VirtualBox起動
## 新しく仮想マシンを作るとき
メモリ、ディスクはお好みで指定する。自マシンのスペックをみつつ設定する。  

アダプター（以降、NICと呼ぶ）は、デフォルトのままだとNATになる。  
ここでいうNATというのはVirtualBox用語。
※各モードの解説は[https://www.it-poem.com/?p=310](https://www.it-poem.com/?p=310)が分かりやすかった。  
→sshで中に入る予定がある場合は、以下の手段が取れる
- NATにてポートフォワーディング設定を行う
- NATとは別にホストオンリーアダプターのNICを作る

以下、NICをNATオンリーで作成した場合の話。

- ゲスト→ホスト→インターネット:アクセス可。
  - yumなど利用する場合は問題なし
- ホスト→ゲストへのアクセス:アクセス不可。
  - sshでホストからVirtualBoxの中へ入りたい場合は、ちょっと設定が必要(ポートフォワーディング)  
  [https://qiita.com/zaburo/items/963a436d8a247eb6dbc3](https://qiita.com/zaburo/items/963a436d8a247eb6dbc3)
- プロキシの設定などは、OSセットアップが終わってから通常のLinuxと同じように設定する

## isoを入れるまえに
デフォルトの状態でインストール作業をすると、ちょっとウィンドウが狭い。  
isoを入れる前に、ディスプレイサイズを指定するパラメータを追加すると作業しやすい。  
「[virtualbox centos 画面 見切れる](https://www.google.com/search?q=virtualbox+centos+%E7%94%BB%E9%9D%A2+%E8%A6%8B%E5%88%87%E3%82%8C%E3%82%8B)」で検索すると対処法が出て来る。
