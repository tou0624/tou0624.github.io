---
layout: post
title:  "JVMのメモリ管理方法の基礎知識"
categories: JVM
---

# 目的
JVMの基本的な構造を理解するために記載する。

# 構造
## 前提
- JVMは、OSから見るとプロセスの1つ。なのでJVMが利用するメモリは、プロセスがメモリを使うのと同じような形になる。(Javaプロセスのユーザ空間内に割り当て)

## 構造
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image1.svg)

| 名前 | メモ |
| --- | --- |
| Javaヒープ | オブジェクトや、ロードしたクラス情報など置いておく場所。 |
| Cヒープ | JNI(Java Native Interface)などを置いておく場所。 |
| スレッドスタック | スレッド情報を置いてく場所。 |

参考  
[http://itdoc.hitachi.co.jp/manuals/link/cosmi_v0950/03Y0430D/EY040139.HTM](http://itdoc.hitachi.co.jp/manuals/link/cosmi_v0950/03Y0430D/EY040139.HTM
)

### Javaヒープの内部
#### Java7まで
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image2.svg)

#### Java8以降
Permanent領域→Metaspace領域へ変化。  
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image5.svg)

- サイズ指定用パラメータ：MetaspaceSize, MaxMetaspaceSize
- Permanent関連の指定は無視される
- デフォルト値がint最大値なのでほぼ無限なので、実際に運用する際はパラメータの指定に注意が必要。

### Javaヒープ領域のnew, old領域の使い方
※図はJava7までのものを利用しているが、Java8以降も同じ  
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image3.svg)

| 分類 | 領域名 | 説明 |
| --- | --- | --- |
| new | Eden | newしたオブジェクトが格納される |
| new | Survivor | new領域のみのGC(Scanvenge GC)実施時に生き残ったオブジェクトを格納する領域。|
| old | Tenured | new領域のみのGC(Scanvenge GC)で、所定の回数生き残ったオブジェクトを置いておく領域。|

※new(Eden, Survivor)はXmnパラメータでサイズ指定可能

参考  
[https://software.fujitsu.com/jp/manual/manualfiles/M090097/B1WN9561/03Z200/B9561-00-07-01-05.html](https://software.fujitsu.com/jp/manual/manualfiles/M090097/B1WN9561/03Z200/B9561-00-07-01-05.html)  

### Javaヒープ領域付近のもの(Permanent/Metaspace)
| 時期 | 領域名 | 説明 |
| --- | --- | --- |
| ~Java7 | Permanent | クラス情報、静的情報、定数などを置いておく領域。Javaヒープに置く|
| Java8~ | Metaspace | クラス情報を置いておく領域。ネイティブメモリに置く|


### GCについて
GC(ガベージコレクション)は2種類ある。
※GCは未使用オブジェクトなどをメモリ上から掃除する機能のこと

| 名前 | 説明 |
| --- | --- |
| Scavenge GC | new領域(Eden+Survivor)のみのGC |
| Full GC | new(Eden+Survivor)+old(Tunered)+Permenent領域すべてを対象にしたガベージコレクション(GC) |

Permanent領域もFull GC対象になることに少し驚いた。

#### GC対象(~Java7)
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image4.svg)

#### GC対象(Java8~)
![画像](https://tou0624.github.io/assets/study-jvm-memory-management/image6.svg)

Scavenge GCにてnew領域の中を移動して、利用していないオブジェクトは濾されていく模様。  
オブジェクトは濾された回数を保持しており、規定の回数を超えたらold領域へ移動できる。  
※規定の回数は「MaxTenuringThreshold」にて指定  
→この手のGCを「世代別GC」と呼ぶ模様。

詳細なScavenge GCの動作については、以下サイトがわかりやすい。  
[https://www.atmarkit.co.jp/ait/articles/0504/02/news005.html](https://www.atmarkit.co.jp/ait/articles/0504/02/news005.html)

もしnew→old領域(Tenured領域)へオブジェクトを移動するタイミング設定(=MaxTenuringThresholdの設定)が適切でない場合。  
長期保管すべきでないオブジェクトがold領域に溢れ、Full GCが頻繁に起こる。  

### JVMにてメモリリーク(OutOfMemoryError)発生時に着目する観点
- このサイトが分かりやすい。   
[https://www.itmedia.co.jp/enterprise/articles/0907/10/news002.html](https://www.itmedia.co.jp/enterprise/articles/0907/10/news002.html)
  - GCログを見る
  - 動作を確認して絞り込み
