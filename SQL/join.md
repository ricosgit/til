# 結合
<br>

## 結合とは
UNION句やINTERSECT句のような集合演算はテーブルの行方向に作用する（行が増えたり減ったりする）のに対し、
結合はテーブルの列方向に作用する（列が増えたり減ったりする）。
集合演算については[集合演算について](./set_operation.md)を参照。
<br>

## 内部結合 -- INNER JOIN苦
以下の2テーブルが存在しているとして、この2つを一つのテーブルにまとめたい。
Productsテーブル
|id|name|
|--|--|
|0001|シャツ|
|0002|コピー用紙|
|0003|カッター|
|0004|フォーク|
|0005|ボールペン|
|0006|手袋|

shopsテーブル
|id|name|product_id|
|--|--|
|1|札幌|0001|
|2|札幌|0002|
|3|東京|0003|
|4|大阪|0004|
|5|沖縄|0005|

以下のSQLで実現できる。
```sql
SELECT S.id AS shop_id, S.name AS shop_name, S.product_id, P.name AS product_name
    FROM shops AS S INNER JOIN products AS P
    ON S.product_id = P.id;
```
とすると、
|shop_id|shop_name|product_id|product_name|
|--|--|--|--|
|1|札幌|0001|シャツ|
|2|札幌|0002|コピー用紙|
|3|東京|0003|カッター|
|4|大阪|0004|フォーク|
|5|沖縄|0005|ボールペン|

という結果が返ってくる。
<br>

## 外部結合 -- OUTER JOIN苦
内部結合の場合は指定の値が存在するレコードのみを抽出する。（上の例だとshops.product_idに存在しない「0006:手袋」は抽出されない。）
が、OUTER JOINを使用して以下のように実行すると、
```sql
SELECT S.id AS shop_id, S.name AS shop_name, S.product_id, P.name AS product_name
    FROM shops AS S RIGHT OUTER JOIN products AS P
    ON S.product_id = P.id;
```
|shop_id|shop_name|product_id|product_name|
|--|--|--|--|
|1|札幌|0001|シャツ|
|2|札幌|0002|コピー用紙|
|3|東京|0003|カッター|
|4|大阪|0004|フォーク|
|5|沖縄|0005|ボールペン|
|||0006|手袋|

という結果が返り、片方のテーブル内のレコード全てを表示していることがわかる。
この場合、`RIGHT OUTER JOIN` と指定しているため、sql文の右側、つまりproductsテーブルの中身を全て表示するよう指定している。
<br>

## クロス結合（直積） -- CROSS JOIN句
2つのテーブルのレコードについて、全ての組み合わせを作成する結合方法。
なので
```sql
SELECT S.id AS shop_id, S.name AS shop_name, S.product_id, P.name AS product_name
    FROM shops AS S CROSS JOIN products AS P
```
というようなsql文を実行すると、割ととんでもない数のレコード数が表示される。

その性質を利用して、大量にテストデータを作成する手法もある。
詳しくは[こちら](https://qiita.com/cobot00/items/8d59e0734314a88d74c7)を参照。
<br>

## 所感
果たして使い道はあるのだろうか？
pre環境と本番環境の同テーブルの中身に違いがないか調べる時とかに使えるか。。。？

## 参考
[ゼロからはじめるデータベース操作 SQL : 231~239P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452
[SQLで大量のテストデータ作成 - Qiita]https://qiita.com/cobot00/items/8d59e0734314a88d74c7