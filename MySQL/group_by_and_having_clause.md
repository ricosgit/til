# GROUP BY句、HAVING句について
<br>

## GROUP BY句
<br>

### 実行順序
以下のようなgroup by/where句を使用したSQLを実行したとき、
```sql
SELECT column, COUNT(*)
    FROM table
WHERE column = 'xxx'
GROUP BY column;
```
その実行順序は

```
FROM → WHERE → GROUP BY → SELECT
```
となる。
記述順に
```
// これは間違い
SELECT → FROM → WHERE → GROUP BY
```
と実行されるわけではないので注意。
<br>

### GROUP BY使用時にSELECT文にかけるもの
- 定数（文字列や数値）
- 集約関数（max(), count()等）
- GROUP BY句で指定した列名（集約キー）
これ以外を指定するとエラーになる。
<br>

## HAVING句
<br>

### 使い方
GROUP BY句を使用してグループ分けをした後、そのグループに対して条件を指定したい時に使用できる。
※WHERE句はレコードに対しての条件指定しかできないため、グループに対して条件指定したい場合はHAVIN句を使用する。
指定できるのはSELECT文と同じく以下3つ。
- 定数（文字列や数値）
- 集約関数（max(), count()等）
- GROUP BY句で指定した列名（集約キー）

例）
集約した結果のレコード数が2行になるようなグループを取得したい
```sql
SELECT column1, COUNT(*)
    FROM table
GROUP BY column1
HAVING COUNT(*) = 2;
```
<br>

### WHERE句とHAVING句
集約キーに関する条件はWHERE句、HAVING句のどちらでも指定できる。
正し、この場合はWHERE句を使用する方が良い。

理由は2つあり、
1. 役割の違い<br>
HAVING句は「グループ」に対する条件指定が役割なので、「レコード」に対する指定はWHERE句を使用すべき。

2. 処理速度の違い<br>
HAVING句はソート（重い処理）を行って、グループ化された後に実行されるのに対し、
WHERE句は条件絞り込みを行った後にソートを行うので、ソート対象のレコード数を減らすことができる。
また、WHERE句はインデックスを使用して処理の高速化が可能。


