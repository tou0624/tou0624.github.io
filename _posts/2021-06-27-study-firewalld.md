---
layout: post
title:  "firewalldでのホワイトリスト形式アウトバウンド設定"
---

firewalld(CentOS7)にてホワイトリスト形式にて、アウトバウンドのファイアウォール設定して若干ハマったのでメモ。  
TCPアクセスのみ対象。firewalldのダイレクトルール適用にて設定。  
**参照元: [https://qiita.com/akagane99/items/cd43af59dada0e48ef59](https://qiita.com/akagane99/items/cd43af59dada0e48ef59)**

# 手順 
## 1_firewall-cmdを利用し、ダイレクトルールを利用しホワイトリスト形式でアウトバウンド指定
- rootユーザで作業
- `systemctl status firewalld`にて、firewalldコマンドが生きているかチェック
- インバウンド設定は別途必要だけど今回は省略。

### 最初に、アクセスを許可するIPを指定する。
```
# firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 1 -d xxx.xxx.xxx.xxx/32 -p tcp -o [適用させたいNIC名] -j ACCEPT
```

NICを個別指定する必要がある場合、`ip addr` にて突き止める。指定しなくても動く  
実際の設定内容はiptablesの中身と同じ。firewalldのルールに従った記載が必要なのが少しややこしい。  
詳細な設定はfirewalldのmanやfirewalldのWikiを読むと載っている🙆‍♀️  
[https://firewalld.org/documentation/man-pages/firewall-cmd.html](https://firewalld.org/documentation/man-pages/firewall-cmd.html)  
[https://fedoraproject.org/wiki/Firewalld?rd=FirewallD#Direct_options](https://fedoraproject.org/wiki/Firewalld?rd=FirewallD#Direct_options)

※IP欄のCIDRは自マシンの所属するネットワークの様子を見て判断  

### 次に、エフェメラルポートの開放及びループバックアドレスの許可を実施する。
```
# firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 10 -p tcp --dport [ポート開始]:[ポート終了] -j ACCEPT
# firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 18 -p tcp -d 127.0.0.0/32 -j ACCEPT
```
OSによってエフェメラルポートが異なるので調査し、該当ポートをあける。  
ここをあけておかないと、通信相手のレスポンスを受け取ることができなくなってしまう。  
ここの設定をし忘れると例えばssh接続ができなくなりサーバがある場所へ泣く泣く出社したり、ローカルから中にあるDBへアクセスできなくなったりする・・かも。    
（参考）[https://djeeeno.blogspot.com/2018/05/20180504-01-drop-outbound.html](https://djeeeno.blogspot.com/2018/05/20180504-01-drop-outbound.html)

※IP欄のCIDRは自マシンの所属するネットワークの様子を見て判断  

### 最後に、全ての通信を遮断する。
```
# firewall-cmd --permanent --direct --add-rule ipv4 filter OUTPUT 19 -p tcp-o [適用させたいNIC名] -j REJECT
```

全て遮断する。優先順位は今までのルールの中で一番低めにしておく

## 2_設定反映
```
# firewall-cmd --reload
# firewall-cmd --direct --get-all-rules
```
firewalld経由でiptablesへルール設定を実施する。  
リロードすると `/etc/firewalld/dirext.xml` が自動で書き換わる。(--permanentオプションがついている場合のみ)  
設定ミスったからアウトバウンドのルールを急ぎ取り払いたい場合は、direct.xmlをリネーム退避後`firewalld-cmd --reload`すればOK

# メモ
## (そもそも) ファイアウォールの基礎知識
CentOS6までは「iptables」を利用していたが、CentOS7からは「firewalld」を利用する。    
上記いずれも、Linuxカーネルに組み込まれているNetfilter機能へのインターフェース（←この辺はあまり気にしなくても、とりあえずファイアウォール設定したいだけなら問題ない）  

ゾーン（適用範囲の単位のようなもの）をNICへ適用する。  
この概念はインバウンドのみ利用可能で、アウトバウンドのルール適用は ダイレクトルール or リッチルール 適用となる。
![画像](https://tou0624.github.io/assets/study-firewalld/image1.svg)

### NetFilter機能に関する用語
- チェイン
  - パケットフィルタリングを実施する流れ
  - INPUTチェイン、OUTPUTチェイン、FORWARDチェインの3つが存在する。
  - INPUTチェイン≒インバウンド、みたいなイメージ

### iptablesに関するメモ
- 各チェインに対して、デフォルト設定+個別設定できるようになっている。
- デフォルト設定は パケットのACCEPT(許可) / DROP(廃棄) となる。
- ルールは再起動すると消えてしまうので、永続性を持たせるためには`/etc/sysconfig/iptables`へルール記載が必要。

# 参考
- [https://www.atmarkit.co.jp/ait/articles/1602/18/news019_2.html](https://www.atmarkit.co.jp/ait/articles/1602/18/news019_2.html)
- [https://knowledge.sakura.ad.jp/22269/](https://knowledge.sakura.ad.jp/22269/)
- [https://qiita.com/akagane99/items/cd43af59dada0e48ef59](https://qiita.com/akagane99/items/cd43af59dada0e48ef59)
