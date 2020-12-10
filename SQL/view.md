# ビュー
<br>

## ビューとは
SELECT文をまるまるクリップボードに保存できるような機能。
ビューは参照される度に仮装テーブルを作成する。

メリットとしては2つ。
1. データを保存せず、SELECT文のみを保存するため、記憶装置の要領を節約できる
2. よく使うSELECT文を保存しておくことで、使い回しが効く
<br>

## 使用方法
<br>

### 保存
```sql
CREATE VIEW ビュー名 (ビューの列名1, ビューの列名2, ...)
AS
SELECT文
```
具体的にはこんな感じ
```sql
CREATE VIEW products_sum (category, count, ...)
AS  // 必須
SELECT category, COUNT(*)  // このSELECT文が保存される
    FROM products
    GROUP BY category;
```
<br>

### 使用
```sql
SELECT category, count
    FROM products_sum // テーブルの代わりにビューを指定する
```
ビューに対して検索をかける場合は以下のようになるが
```sql
SELECT category, count
    WHERE count > 2
    FROM products_sum // テーブルの代わりにビューを指定する
```
この場合の実行順序は
1. ビューに定義されたSELECT文が実行
2. その結果に対して、ビューをFROM句に指定したSELECT文が実行
<br>

### 更新
ビューに対して更新をかけることができるのは以下の条件を満たしている時。
1. SELECT句にDISTINCTが含まれていない
2. FROM句に含まれるテーブルが1つだけ
3. GROUP BY句を使用していない
4. HAVING句を使用していない

ビューを更新すると参照元テーブルのレコードも更新される。
あんまり使う機会はなさそう。
<br>

### 削除
```sql
DROP VIEW ビュー名
```
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 149~160P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452
