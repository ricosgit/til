# ターミナルの出力をクリップボードにコピー

`pbcopy`を使用することでターミナルの出力をクリップボードにコピーすることができる。

1. 直接入力する
```terminal
$ pbcopy
// 入力開始
aaa
bbb
// `Ctrl + D`で終了
```
とすると
```
aaa
bbb
```
がコピーされる。

2. ファイル内容をコピーする
```terminal
$ cat ~/.ssh/id_rsa.pub | pbcopy
```
で指定したファイル（ここでは`~/.ssh/id_rsa.pub`）の内容がコピーされる。

または
```terminal
$ pbcopy < ~/.ssh/id_rsa.pub
```
でも可能。

## 参考
[Macな人は pbcopy が便利 - Qiita]https://qiita.com/mktakuya/items/c57a12b5b072a658acd0
[Mac GitHub SSH接続設定 - Qiita]https://qiita.com/ucan-lab/items/e02f2d3a35f266631f24