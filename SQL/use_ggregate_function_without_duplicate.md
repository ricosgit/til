# 重複を除いたレコード数の取得

## 集積関数とは
```sql
COUNT
SUM
AVG
MAX
MIN
```
等の集計用の関数のこと。

## 結論
重複値を除外して集積関数を使用したい場合は以下のようなSQLを実行する。
```sql
SELECT COUNT(DISTINCT sample_column) FROM sample_table;
```

処理の順序は
1. sample_columnの重複値を除外
2. 行数を数える
となる。

そのため注意点としては、`DISTINCT`は集積関数の引数として渡す。
```sql
SELECT DISTINCT COUNT(sample_column) FROM sample_table;
```
の形はNG。


## 参考
ゼロからはじめるデータベース操作 SQL : 80p