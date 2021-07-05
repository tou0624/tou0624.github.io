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
Javaプロセスで利用するメモリは、Javaヒープ、Cヒープ、スレッドスタックの3領域に分割。  
![画像](https://tou0624.github.io//assets/study-jvm-memory-management/test.svg)

| 名前 | メモ |
| --- | --- |
| Javaヒープ | オブジェクトや、ロードしたクラス情報など置いておく場所。 |
| Cヒープ | JNDIなどを置いておく場所。 |
| スレッドスタック | スレッド情報を置いてく場所。 |

ちゃんとした説明は、以下URLを参照。  
[http://itdoc.hitachi.co.jp/manuals/link/cosmi_v0950/03Y0430D/EY040139.HTM](http://itdoc.hitachi.co.jp/manuals/link/cosmi_v0950/03Y0430D/EY040139.HTM
)

### Javaヒープの内部
Javaヒープの中は更に細かめに別れており、パラメータによるチューニング実施時はこちらの領域が焦点となることが多い。
こんな感じ。
※この辺、説明するサイトによって用語の定義がバラバラなので注意・・・
![画像](https://tou0624.github.io//assets/study-jvm-memory-management/test2.svg)

JVMの各領域には役割があるが、以下の図でオレンジ色の部分がオブジェクトを管理する部分となる。  
※よく見かける「Xmx」などのパラメータの対応範囲もあわせて記載。
![画像](https://tou0624.github.io//assets/study-jvm-memory-management/test3.svg)

各領域の大まかな説明。オブジェクトを効率的に管理するために3つの領域を作り、うまくやっている模様。

| 分類 | 領域名 | 説明 |
| --- | --- | --- |
| new | Eden | newしたオブジェクトが格納される |
| new | Survivor | new領域だけのGC実施時に生き残ったオブジェクトを格納する領域。|
| old | Tenured | 所定の回数、new領域のみのGCで生き残ったオブジェクトを置いておく領域。|
| - | Permanent | クラス、静的情報、定数などを置いておく領域。|

※newとなっている部分は「Xmn」パラメータで大きさ指定できる箇所。

Javaヒープの各領域の説明は、以下が分かりやすい。  
[https://software.fujitsu.com/jp/manual/manualfiles/M090097/B1WN9561/03Z200/B9561-00-07-01-05.html](https://software.fujitsu.com/jp/manual/manualfiles/M090097/B1WN9561/03Z200/B9561-00-07-01-05.html)  
→上記の図で「New世代領域」「Old世代領域」となっている部分は、先ほどの表の「分類」と対応する。

### GCについて
GC(ガベージコレクション)は2種類ある。
※GCは未使用オブジェクトなどをメモリ上から掃除する機能のこと

| 名前 | 説明 |
| --- | --- |
| Scavenge GC | new領域(Eden+Survivor)のみのGC |
| Full GC | new(Eden+Survivor)+old(Tunered)+Permenent領域すべてを対象にしたガベージコレクション(GC) |

Permanent領域もFull GC対象になることに少し驚いた。

GC対象となる領域を図にしてみたものが以下。
![画像](https://tou0624.github.io//assets/study-jvm-memory-management/test4.svg)

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
