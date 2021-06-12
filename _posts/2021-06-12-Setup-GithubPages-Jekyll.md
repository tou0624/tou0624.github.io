---
layout: post
title:  "Github Pagesをブログ形式にするためのセットアップ"
date:   2021-06-12 22:25:17 +0900
categories: GitHubPages
---
Github Pagesを自分用の技術メモスペースにしたく、記事を書くことへ慣れるのも兼ねてセットアップのメモを記載します。  

# 作業内容
- 全体的にGithubの公式アナウンスに従いました。全ては以下サイトを見れば解決
  - [Jekyll を使用して GitHub Pages サイトを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)
  - [Jekyll を使用して GitHub Pages サイトをローカルでテストする](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)
  - [Jekyll を使用して GitHub Pages サイトにコンテンツを追加する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll)
  - [Jekyll を使用して GitHub Pages サイトにテーマを追加する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)
- [jekyllの公式サイト](https://jekyllrb.com/)もある。
- ただHelloWorldを表示させる程度のガイダンスでいいなら、[https://pages.github.com/](https://pages.github.com/) に従って作業すればすぐに終わる。手順通りに作業するとページにHelloWorldと表示される。

## 1_Githubにてリポジトリ作成
- [Jekyll を使用して GitHub Pages サイトを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)を参照
- リポジトリの名前は`(username).github.io`にする。

## 2_ローカル環境へJekyllインストール
- [Jekyll を使用して GitHub Pages サイトを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)を参照

- Jekyllとは何か？
  - Github Pagesにて利用する静的サイトジェネレータ
- Jekyllはgemなので、Rubyインストールが必要。
- gemを管理するツールとしてBundlerというものがオススメらしいのでそちらもインストール（こちらは使わなくてもインストール自体は可）

## 3_ページを作る+ローカルで動かす+プッシュ
- [Jekyll を使用して GitHub Pages サイトを作成する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll)を参照
- bundleコマンドで動くらしい。gem書き換えたらbundle install/bundle updateしないと怒られる場合がある
- ここでgithubへのpushをする手順まで記載があるので、初回コミットが上がってみれるようになる

## 4_記事を追加してみる
- [Jekyll を使用して GitHub Pages サイトにコンテンツを追加する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll)を参照
- _postフォルダの下に`YYYY-MM-DD-記事名.md`でファイル作成+冒頭に要素を書き足せば記事になる模様。ちなみにこの記事は`_posts/2021-06-12-Setup-GithubPages-Jekyll..md`にしており、URLはファイル名をもとに生成されている模様
- ファイル上部にもろもろ記載する必要あり。この記事の場合は以下の通り記載しています
```
---
layout: post
title:  "Github Pagesをブログ形式にするためのセットアップ"
date:   2021-06-12 22:25:17 +0900
categories: GitHubPages
---
```

## 5_デザインを変えたいとき
- [Jekyll を使用して GitHub Pages サイトにテーマを追加する](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/adding-a-theme-to-your-github-pages-site-using-jekyll)を参照
- `_config.xml`にある`#theme: minima`の部分を、自分の好きなテーマに書き換える。
  - ここで書き換えられるのは[Github Pagesで公式提供しているテーマ](https://pages.github.com/themes/)のみ
  - 別のデザインを使いたい場合は、[ここ](https://github.com/topics/jekyll-theme)から好きなデザインを選び、説明を読んで都度導入。
    - 大体`_config.xml`と`Gemfile`を書き換えれば導入できる模様。
    - 実際、導入しようとするとバージョンが云々などの理由でうまくいかないのでまた今度ちゃんとみてみる

## おまけ:ローカルで見え方を確認したい場合
- [Jekyll を使用して GitHub Pages サイトをローカルでテストする](https://docs.github.com/ja/pages/setting-up-a-github-pages-site-with-jekyll/testing-your-github-pages-site-locally-with-jekyll)を参照
- ローカルで動作確認する場合はGemfileを利用する。Gemfileへ何か追記などした場合はbundleコマンドをたたく。場所はこのリポジトリをチェックアウトしたルートの場所
```
$ bundle
```

- ローカルでの起動確認する場合、以下を実施すると http://127.0.0.1:4000/ にて動作確認可能となる。
```
$ bundle exec jekyll serve
```
