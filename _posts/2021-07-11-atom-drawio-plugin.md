---
layout: post
title:  "atomでdrawioを使う場合"
---

プライベートではいつもatomを利用しており、atomでdrawioのプラグインないかなぁと探してみたのでメモ。  
(2021/07/11現在では)VScodeのプラグインの方が使いやすいかなぁと感じる。

# プラグイン
「atom-drawio」というプラグイン。設定→パッケージのインストール→draiwoで検索すると出てくる
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image1.png)

詳細は以下を参照。  
[https://atom.io/packages/atom-drawio](https://atom.io/packages/atom-drawio)

# 使い方
ダウンロードが終わったら、Projectタブのファイル作りたい場所で右クリックする。  
「New Drawio Diagram」と出てくるので、そちらをクリック。  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image2.png)

そうするとsvgファイルの名前をつけるよう促すモーダル？が出てくる。  
ここでsvg以外の拡張子にするとdrawioツールを使えないので注意。  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image3.png)

好きな名前をつけてEnterを押す。  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image4.png)

そうすると見覚えのある画面が出てくるので、思うがままに・・・  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image5.png)

# 感想
<b>タブの×ボタンで閉じることが出来ない</b>のが地味に使いづらい。  
タブの×ボタンを押すと、以下のようなメッセージが出てくる。  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image6.png)

右上にあるExitボタンを押すと閉じることができる。  
![画像](https://tou0624.github.io//assets/atom-drawio-plugin/image7.png)

あとは、<b>このプラグインを経由せず新規に「hoge.drawio」といったファイルを作っても、このプラグインは一切反応してくれない。</b>  
svgファイルしか対象にしていない模様。VScodeのプラグインは反応してくれるので、VScodeのノリで作ろうとすると「あれっ」となる。  

まだ開発途上のプラグインのようなので、今後使いやすくなるといいなぁと願っている。他力本願…⛩

それ以外は普通。Exportはやったことないので試したらまた記載する。
