---
layout: post
title:  "古いcomposerを退避(macOS)"
---

composerを利用しプロジェクトを導入しようとしたら化石状態のcomposerがいたので、一応退避方法をメモする。  
新しいcomposerをhomebrewで入れてパスを通すところをゴールにする。

# 前提
* homebrew導入済み

# 流れ
homebrewを利用してcomposerを導入
```
% brew install composer
```

場所チェック（あとでシンボリックリンク先にするのでメモる）
```
% brew list composer
/usr/local/Cellar/composer/2.0.13/bin/composer
```

現状のcomposerがどこにあるかチェック
```
% which composer
/usr/local/bin/composer
```

退避
```
% cd /usr/local/bin
% mv composer{,.bk}
% ls -la composer*
-rwxr-xr-x  1 xxx  staff  1875478 11 26  2018 composer.bk
```

シンボリックリンクを貼る
```
% ln -s /usr/local/Cellar/composer/2.0.13/bin/composer composer
```

ただしく終わっているか様子見。問題なさそう
```
 % ls -la composer*
lrwxr-xr-x  1 xxx  admin       46 10 18 20:56 composer -> /usr/local/Cellar/composer/2.0.13/bin/composer
-rwxr-xr-x  1 xxx  staff  1875478 11 26  2018 composer.bk
```

最後に`composer -v`などを実行して正常終了していることをチェック

おわり