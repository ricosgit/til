# トランザクション
<br>

## トランザクションとは
複数の処理をまとめた１単位のこと。
その単位でテーブルの更新や削除などが実行できる。
<br>

## MySQL,SQL Server, PostgreSQLのトランザクション使用方法
```sql
// MySQL
START TRANSACTION
    UPDATE products
        SET name = sample;

    UPDATE products
        SET price = 1000;
COMMIT // or ROLLBACK

// SQL Server, PostgreSQL
BEGIN TRANSACTION
    UPDATE products
        SET name = sample;

    UPDATE products
        SET price = 1000;
COMMIT // or ROLLBACK
```
<br>

## Oracle, DB2のトランザクション使用方法
Oracle, DB2では、暗黙にトランザクションが開始されることになっている。
そのため以下のようにトランザクションを実行する。
```sql
// 暗黙にトランザクション開始
UPDATE products
    SET name = sample;

UPDATE products
    SET price = 1000;

COMMIT // or ROLLBACK
```

### 補足
トランザクション開始のパターンは2つあり、
1. 「1つのSQLで1つのトランザクション」（自動コミットモード）
2. ユーザがCOMMITまたはROLLBACKを実行するまでを1つのトランザクションとみなす
一般的なDBMSではどちらかのモードを設定可能になっている。

1がデフォルトで設定されている: MySQL,SQL Server, PostgreSQL
2がデフォルトで設定されている: Oracle, DB2

そのため、1の場合では1つのSQLごとに処理が確定するが、2の場合は明示的に `COMMIT` を実行しないと処理が確定しない。
<br>

## 参考
[ゼロからはじめるデータベース操作 SQL : 135~143P]https://www.amazon.co.jp/SQL-%E7%AC%AC2%E7%89%88-%E3%82%BC%E3%83%AD%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9%E6%93%8D%E4%BD%9C-%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0%E5%AD%A6%E7%BF%92%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA-%E3%83%9F%E3%83%83%E3%82%AF/dp/4798144452
