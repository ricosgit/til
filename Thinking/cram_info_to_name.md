# 名前に情報を詰め込む
<br>

## 明確な単語を選ぶ
例えば
```php
function getPage() {
    ...
}
```
という関数があった時、 `get` というのは抽象的である。
この関数はどこからページをとってくるのかわからない。
ローカルキャッシュから？DBから？インターネットから？
インターネットからであれば `fetchPage` や `downloadPage` などのより明確な単語を使用する。

脳死で名前を決めるのではなく、より適切な単語がないか考える。
例
|単語|代替案|
|--|--|
|send|deliver, dispatch, announce, distribute, route|
|find|search, extract, locate, recover|
|start|launch, create, begin, open|
|make|create, set up, build, generate, compose, add, new|
<br>

## tmpやretvalなどの汎用的な名前を避ける
tmpやretval(return value),resultなどの汎用的な名前は付けるのは楽だが、内容は何も伝わらない。
例えば3の2乗(=9)を表したい場合、sum_squaresなどの名前を付けるべきである。
```php
$sum_squares = 3 * 3;
```
しかし以下のように変数名がretvalとなっていると、
```php
$retval += 3 + 3; // 2乗ではないためバグ
```
バグなのに変数がなんなのか分からないためバグが発見されにくい。
以下のように変数名がしっかり決められているとバグが発見されやすい。
```php
$sum_squares = 3 + 3; // squareなのに2乗されていない！バグやん！
```
<br>

## 具体的な名前を使う
TCP/IPポートをサーバがリッスンできるかを確認するメソッドが会った時、
`ServerCanStart()` だと抽象的で、具体的に何をするメソッドなのかが伝わりづらい。
そのため、この場合は `CanListenOnPort()` などにすると具体的な動作が伝わりやすい。
<br>

## 変数名に大切な情報を追加する
1. 値の単位
返り値が時間の場合はその単位が秒なのか、ミリ秒なのか
返り値がファイルサイズの場合はその単位がmbなのかkbなのか等、必要な情報を変数名に含める。
```php
$start_sec = (new Carbon())->second; // 変数名がstartだと単位が分からない。
```

例
|変数名|代替案|
|--|--|
|delay|delay_sec|
|size|size_mb|
|limit|max_kbps|
|angle|degrees_cw|

2. その他重要情報
エスケープ処理前のデータなど、安全ではないデータであることを示す情報を変数名に含める。
|状況|変数名|
|--|--|
|passwordはプレインテキストなので、処理前に暗号化すべき|plaintext_password|
|エスケープ前のコメント|unescaped_comment|
|htmlの文字コードをUTF-8に変換した|html_utf8|
|urlエンコード済のデータ|data_urlenc|
<br>

## スコープの大きな変数には長い名前をつける
スコープが小さい場合、例えば以下のような場合
```php
if ($debug) {
    $m = 0;
    sampleFunc($m);
    return $m;
}
```
だと、スコープが非常に小さく、処理の全体が見えるので変数名が短くても把握できる。

しかしスコープが大きくなると、**急に$mって変数が出てきた！なんだこれ！** となってしまうため、
スコープが大きい変数に関しては十分な情報を詰め込んだ変数名を付けるべきである。
<br>

## 大文字やアンスコなどに意味を含める
クラス名はキャメルケース、変数名はアンスコで区切る、メンバ変数は末尾にアンスコを付けるなどして
それぞれで違うフォーマットを使用することでコードの理解を早めることができる。
```php
// クラス名
class LogReader // キャメルケース
{
    $sample_ = 'sample'; // 末尾にアンスコ

    function sampleFunc()
    {
        $func_sample = 'func_sample': // アンスコ区切り
    }
}
```
<br>

## 参考
