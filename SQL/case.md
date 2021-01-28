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

## 注意点
- 各分岐が返すデータ型を統一する
- ENDの書き忘れに注意
- ELSE句は指定なしだと「ELSE NULL」が指定されるが、わかりづらいので明記しておく

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

## より実践的な例

### 既存コード体型を新しい体型に変換して集計
以下のようなテーブルを地方別に集計したい。

PopTbl
|pref_name|population|
|--|--|
|徳島|100|
|香川|200|
|愛媛|150|
|高知|200|
|福岡|300|
|佐賀|100|
|長崎|200|
|東京|400|
|群馬|50|

```sql
SELECT CASE pref_name
        WHEN '徳島' THEN '四国'
        WHEN '香川' THEN '四国'
        WHEN '愛媛' THEN '四国'
        WHEN '高知' THEN '四国'
        WHEN '福岡' THEN '九州'
        WHEN '佐賀' THEN '九州'
        WHEN '長崎' THEN '九州'
    ELSE 'その他' END AS district,
    SUM(population)
FROM PopTbl
GROUP BY CASE pref_name
        WHEN '徳島' THEN '四国'
        WHEN '香川' THEN '四国'
        WHEN '愛媛' THEN '四国'
        WHEN '高知' THEN '四国'
        WHEN '福岡' THEN '九州'
        WHEN '佐賀' THEN '九州'
        WHEN '長崎' THEN '九州'
    ELSE 'その他' END;
```
とすると。以下の結果が返る。
|地方名|人口|
|--|--|
|四国|650|
|九州|600|
|その他|450|

ただ、上記だと冗長なので、PostgleSQL,MySQLなど限定で以下のように省略できる。
```sql
SELECT CASE pref_name
        WHEN '徳島' THEN '四国'
        WHEN '香川' THEN '四国'
        WHEN '愛媛' THEN '四国'
        WHEN '高知' THEN '四国'
        WHEN '福岡' THEN '九州'
        WHEN '佐賀' THEN '九州'
        WHEN '長崎' THEN '九州'
    ELSE 'その他' END AS district,
    SUM(population)
FROM PopTbl
GROUP BY district;
```
<br>

### 異なる条件の集計を1つのSQLで行う
以下のようなテーブルから、男女別に人口を集計したい。

PopTbl2
|pref_name|sex|population|
|--|--|--|
|徳島|1|60|
|徳島|2|40|
|香川|1|100|
|香川|2|100|
|愛媛|1|100|
|愛媛|2|50|
|高知|1|100|
|高知|2|100|
|福岡|1|100|
|福岡|2|200|
|佐賀|1|20|
|佐賀|2|80|
|長崎|1|125|
|長崎|2|125|
|東京|1|250|
|東京|2|150|

```sql
SELECT pref_name,
    -- 男性人口
    SUM ( CASE WHEN sex = '1' THEN population ELSE 0 END ) AS cnt_m,
    -- 女性人口
    SUM ( CASE WHEN sex = '2' THEN population ELSE 0 END ) AS cnt_f,
FROM PopTbl2
GROUP BY pref_name;
```
とすると、以下の結果が返る。
|県名|男|女|
|--|--|--|
|徳島|60|40|
|香川|100|100|
|愛媛|100|50|
|高知|100|100|
|福岡|100|200|
|佐賀|20|80|
|長崎|125|125|
|東京|250|150|

注意点として、SUM関数を使用しない場合はレコードの集約が行われないので、
以下のような結果が返ってしまう。
**レコードを集約したい場合は集約関数を使用することを忘れずに**
|県名|男|女|
|--|--|--|
|徳島|60|0|
|徳島|0|40|
|香川|100|0|
|香川|0|100|
|...|...|...|
<br>

### 条件を分岐させたUPDATE
Salaries
|name|salary|
|--|--|
|相田|300,000|
|神崎|270,000|
|木村|220,000|
|斉藤|290,000|


上記テーブルに対し、
1. 現在の給料が30万円以上の社員は10%の減給
2. 現在の給料が25万円以上28万円未満の社員は20%の昇給

としたい場合、
```sql
-- 条件1
UPDATE Salaries
    SET salary = salary * 0.9
    WHERE salary >= 300000;

-- 条件2
UPDATE Salaries
    SET salary = salary * 1.2
    WHERE salary >= 250000 AND salary < 280000;
```
としてしまうと、
条件1の更新で、相田の給料が 300,000 * 0.9 = 270,000 となり、
条件2の対象となってしまう。
そのため、一度にUPDATEを実行したい。
CASE式を使用すると以下のようにして書ける。
```sql
UPDATE Salaries
    SET salary = WHEN salary >= 300000
                 THEN salary * 0.9
                 WHEN salary >= 250000 AND salary < 280000
                 THEN salary * 1.2
            ELSE salary END;
```
**CASE文を使うと、複数のUPDATE文を一つにまとめることができる。**
また、同時に実行されるため、ユニーク制約付きの値も更新できる。
<br>

### CASE式の中で集約関数を使う
|std_id<br>（学生番号）|club_id<br>（クラブID）|club_name<br>（クラブ名）|main_club_flg<br>（主なクラブフラグ）|
|--|--|--|--|
|100|1|野球|1|
|100|2|吹奏楽|0|
|200|2|吹奏楽|0|
|200|3|バドミントン|1|
|200|4|サッカー|0|
|300|4|サッカー|0|
|400|5|水泳|0|
|500|6|囲碁|0|

1. 1つだけのクラブに所属している学生は、そのクラブIDを取得する
2. 複数のクラブを掛け持ちしている学生は、主なクラブIDを取得する

```sql
-- 条件1
SELECT std_id, MAX(club_id) AS main_club
    FROM StudentClub
    GROUP BY std_id
    HAVING COUNT(*) = 1;

-- 条件2
SELECT std_id, club_id AS main_club
    FROM StudentClub
    WHERE main_club_flg = 1;
```
このSQLでも条件を満たす結果は得られるが、複数SQLを発行することになり、パフォーマンスの問題がある。
CASE式を使うとこれを1つにまとめることができる。

```sql
SELECT std_id,
       CASE WHEN COUNT(*) = 1 -- 1つのクラブに所属
            THEN MAX(club_id)
       ELSE MAX(CASE WHEN main_club_flg = 1
                     THEN club_id
                ELSE NULL END) END AS main_club
    FROM StudentClub
    GROUP BY std_id;
```
結果はこんな感じ
|std_id|main_club|
|--|--|
|100|1|
|200|3|
|300|4|
|400|5|
|500|6|



## 参考
[ゼロからはじめるデータベース操作 SQL : 220~224P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452

[達人に学ぶSQL徹底指南書 第2版 : 2~22P]https://www.amazon.co.jp/%E9%81%94%E4%BA%BA%E3%81%AB%E5%AD%A6%E3%81%B6SQL%E5%BE%B9%E5%BA%95%E6%8C%87%E5%8D%97%E6%9B%B8-%E7%AC%AC2%E7%89%88-%E5%88%9D%E7%B4%9A%E8%80%85%E3%81%A7%E7%B5%82%E3%82%8F%E3%82%8A%E3%81%9F%E3%81%8F%E3%81%AA%E3%81%84%E3%81%82%E3%81%AA%E3%81%9F%E3%81%B8-CodeZine-BOOKS/dp/4798157821