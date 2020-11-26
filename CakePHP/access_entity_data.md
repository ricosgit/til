# エンティティデータへのアクセス

## エンティティのデータを取得する
エンティティのデータは以下のように取得することができる。
```php
use App\Model\Entity\Article;

$article = new Article;
$article->title = 'This is my first post';
echo $article->title;
```

また、以下のようにgetメソッドを使用することもできる。
```php
$article->get('title');
```


## エンティティへのデータ追加
既存のエンティティへデータを追加するにはsetメソッドを使用する。
```php
$article->set('title', 'This is my first post');
```

## 参考
[エンティティー - 3.8]https://book.cakephp.org/3.next/ja/orm/entities.html#id4