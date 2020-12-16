# GROUPING演算子
<br>

## GROUPING演算子とは
GROUP BY句を使用した際に、各グループごとの値だけでなく全合計を表示させたい場合に使える。
例を挙げると、GROUPING演算子を使用しない場合は
|キッチン用品|11180|
|--|--|
|衣服|5000|
|事務用品|600|

となるのに対し、GROUPING演算子を使用すると
|合計|16780|
|--|--|
|キッチン用品|11180|
|衣服|5000|
|事務用品|600|

のような結果を出すことができる。
<br>

## GROUPING演算子の種類
- ROLLUP
- CUBE
- GROUPING SETS
<br>

## ROLLUP
### 使い方
```sql
SELECT category, SUM(price) AS sum_price
    FROM products
    GROUP BY ROLLUP(category);
```

※ MySQLで実行する場合は以下
```sql
SELECT category, SUM(price) AS sum_price
    FROM products
    GROUP BY category WITH ROLLUP;
```

とすると、
|category|sum_price|
|--|--|
||16780 //超集合行|
|キッチン用品|11180|
|衣服|5000|
|事務用品|600|

のように出力される。
いわゆる全合計を表す行は**超集合行**と呼ばれる。

### GROUPING関数
上記の通り、超集合行はGROUP BYで指定したカラムの値はNULLになってしまう。
それを防ぐため、引数にとった列の値が超集合行のNULLなら1を、それ以外なら0を返す**GROUPING関数**が用意されている。
（MySQLには多分ない）

これを使って以下のようなSQLを実行すると
```sql
SELECT CASE WHEN GROUPING(category) = 1
        THEN 'カテゴリ合計'
        ELSE category END AS category,
       SUM(price) AS sum_price
    FROM products
GROUP BY ROLLUP(category);
```
|category|sum_price|
|--|--|
|カテゴリ合計|16780 //超集合行|
|キッチン用品|11180|
|衣服|5000|
|事務用品|600|

というように超集合行のcategoryを指定することができる。
<br>

## CUBE
よくわからん、追記予定
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 279~291P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452