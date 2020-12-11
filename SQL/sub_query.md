# サブクエリ
<br>

## サブクエリとは
SELECT文をそのままFROM句に持ち込んだ形のこと。
使い捨てのビューとも言える。

メリットとしては2つ。
1. データを保存せず、SELECT文のみを保存するため、記憶装置の要領を節約できる
2. よく使うSELECT文を保存しておくことで、使い回しが効く
<br>

## 使用方法

ビューが以下のように使用するのに対し、
```sql
// ビューの作成
CREATE VIEW products_sum (category, count, ...)
AS
SELECT category, COUNT(*)
    FROM products
    GROUP BY category;

// 使用
SELECT category, count
    FROM products_sum
```
サブクエリを使用した場合は以下のようになる。
```sql
SELECT category, count
    FROM (SELECT category, COUNT(*)
            FROM products
            GROUP BY category) AS products_sum;
```
実行結果としてはどちらも同じ。
実行順序はビューと同じく
```
サブクエリのSELECT文 → サブクエリをFROM句に指定したSELECT文
```
となる。

サブクエリ内のFROM句にサブクエリを使用して入れ子構造にすることも可能。
<br>

## スカラ・サブクエリ
スカラ = 単一
1行1列だけの戻り値を返すサブクエリのこと。
例えば「10」や「'text'」といった単一の値

例えば「価格が平均以上の商品を取得したい」と言う場合に、
```sql
SELECT *
    FROM products
    WHERE price > AVG(price)； // WHERE句に集積関数を記述することはできないためエラー
```
とするとエラーになってしまう。こういった時にスカラ・サブクエリが使用できる。
```sql
SELECT *
    FROM products
    WHERE price > SELECT (AVG(price)
                        FROM products)；// このサブクエリからは平均単価（数値）が返される
```

また、こういった性質上、SELECT句、GROUP BY句,HAVING句,ORDER BY句とほとんどの場所で使用することができる。
<br>

## 相関サブクエリ
上記のスカラ・クエリをクループごとに使用したいような時に使用できる。
例えば以下のような商品テーブルがあった場合に、

|id|category|name|price|
|--|--|--|--|
|1|衣類|シャツ|1000|
|2|衣類|パンツ|2000|
|3|キッチン用品|フライパン|1500|
|4|キッチン用品|包丁|1000|
|4|キッチン用品|フォーク|500|
|4|事務用品|コピー用紙|500|
|4|事務用品|ホッチキス|200|

各カテゴリの平均以上の商品を取得したい、つまり
「衣類」カテゴリのうち、price >= (1000+2000)/2=1500 の商品
「キッチン用品」カテゴリのうち、price >= (1500+1000+500)/3=1000 の商品
「事務用品」カテゴリのうち、price >= (500+200)/2=350 の商品
をそれぞれ取得したい場合、以下のようなSQLを実行することで実現できる。
```sql
SELECT category, name, price
    FROM products AS S1
    WHERE price > SELECT (AVG(price)
                        FROM products AS S2
                        WHERE S1.category = S2.category // この条件を追加することで「各商品の価格と平均価格の比較を、同じ商品分類の中で行う」となる
                        GROUP BY category);
```
結果はこちら
|category|name|price|
|--|--|--|
|衣類|パンツ|2000|
|キッチン用品|フライパン|1500|
|キッチン用品|包丁|1000|
|事務用品|コピー用紙|500|
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 161~175P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452
