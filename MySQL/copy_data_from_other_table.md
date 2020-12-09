# 別テーブルからデータをコピー
<br>

## 結論
以下のようなコピー用テーブルに
```sql
CREATE TABLE products_copy
(
    id UNSIGNED INT NOT NULL,
    name varchar(255) NOT NULL,
    category varchar(255) NOT NULL,
    price INTEGER,
    created_at DATETIME,
    updated_at DATETIME,
    PRIMARY KEY id
);
```
以下の別テーブルのデータをコピーする場合
```sql
CREATE TABLE products
(
    id UNSIGNED INT NOT NULL,
    name varchar(255) NOT NULL,
    category varchar(255) NOT NULL,
    price INTEGER,
    created_at DATETIME,
    updated_at DATETIME,
    PRIMARY KEY id
);
```

以下のようなSQLで実行できる。
```sql
INSERT INTO products_copy (id, name, category, price, created_at, updated_at)
SELECT id, name, category, price, created_at, updated_at
    FROM products;
```
これを実行すると、仮にproductsテーブルに10行のデータが入っていた場合、そのまま10行がproducts_copyテーブルにINSERTされる。
<br>

## 応用
<br>

WHERE句やGROUP BY句等を使用することもできる。
<br>

```sql
CREATE TABLE products_category
(
    category varchar(255) NOT NULL,
    sum_price INTEGER,
    PRIMARY KEY category
);
```
上記のように商品分類ごとに販売額の合計を集計する、集計用のテーブルにINSERTしたい場合は、
以下のようなSQLで実行することができる。

```sql
INSERT INTO products_category (category, sum_price)
SELECT products, SUM(price)
    FROM products
    WHERE category IS NOT NULL
    GROUP BY category;
```
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 120~123P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452