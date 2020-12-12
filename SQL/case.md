# CASE式
<br>

## CASE式とは
SQL上で条件分岐を行うことができる式。
いわゆるSQL版IF文。
<br>

## 構文
```sql
CASE WHEN 評価式 THEN 式
     WHEN 評価式 THEN 式
     WHEN 評価式 THEN 式
      ...
     ELSE 式
END
```
具体的にはこんな感じ。
```sql
SELECT name,
       CASE WHEN category = '衣類'
            THEN CONCAT('A:', category) // MySQL以外の場合は `||` 文字列結合
            WHEN category = '事務用品'
            THEN CONCAT('B:', category) 
            WHEN category = 'キッチン用品'
            THEN CONCAT('C:', category) 
            WHEN 評価式 THEN 式
           ...
           ELSE NULL // 省略可能だが、わかりやすくするため書いた方が良
       END AS abc_products_category
FROM products
```
結果はこう
|name|abc_products_category|
|--|--|
|シャツ|A:衣類|
|包丁|C:キッチン用品|
|コピー用紙|B:事務用品|
|...|...|
<br>

## CASE文の使いどころ
CASE文は基本的にどこでも使用できるが、SELECT文で使用するのが便利。
**SELECT文の結果を組み替えたい時**に特に使える。
```sql
SELECT category, SUM(price)
    FROM products
GROUP BY category
```
とすると以下のような結果が帰ってくる
|category|price|
|--|--|
|衣類|5000|
|事務用品|600|
|キッチン用品|1100|

が、これを1列で表示したい場合、CASE文を使って
```sql
SELECT SUM(CASE WHEN category = '衣類'
                THEN price ELSE 0 END) AS sum_price_cloth,
       SUM(CASE WHEN category = 'キッチン用品'
                THEN price ELSE 0 END) AS sum_price_kitchen,
       SUM(CASE WHEN category = '事務用品'
                THEN price ELSE 0 END) AS sum_price_office
FROM products;
```
と言うSQLを実行すると
|sum_price_cloth|sum_price_kitchen|sum_price_office|
|--|--|--|
|5000|600|1100|

のように1列で結果を返すことができる。
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 220~224P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452
