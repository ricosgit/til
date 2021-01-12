# コードの美しさ

コードが綺麗に整頓されていると、コードの理解が抜群に早まる。
逆に汚いと、読む気もなくすし理解速度も落ちる。
<br>


## にているコードは似ているシルエットにする
似たような引数をもつコード、似た処理を行っているコードに対しては
改行位置などを統一することで美しく見せることができる。
<br>


## まとめられるコードはメソッドとしてまとめる
似たような処理がある時、一つのメソッドにまとめられない時にはシルエットを整えるべきだが
まとめられる場合はまとめるべき。
<br>


## 縦の線を真っ直ぐにする
以下のように繰り返し同じメソッドを呼び出しているような場合、
```php
CheckFullName('Doug Adams', 'Mr. Douglas Adams', '');
CheckFullName(' Jake Brown', 'Mr. jake Brown III', '');
CheckFullName('No Such Guy', '', 'no match found');
CheckFullName('John', '', 'more than one result');
```
このままだとそれぞれの引数が第2引数なのか、第3引数なのかがわかりづらい。
そのため、縦の線を揃えてやると、、
```php
CheckFullName('Doug Adams' , 'Mr. Douglas Adams' , '');
CheckFullName(' Jake Brown', 'Mr. jake Brown III', '');
CheckFullName('No Such Guy', ''                  , 'no match found');
CheckFullName('John'       , ''                  , 'more than one result');
```
となり、第2,3引数がわかりやすくなる。
<br>


## 一貫性と意味のある並び
以下のようなコードがあった場合、
```php
$details = $this->request->data['details'];
$location = $this->request->data['location'];
$phone = $this->request->data['phone'];
$email = $this->request->data['email'];
$url = $this->request->data['url'];
```
プログラム上はどんな並び順でも問題ないが、ランダムではなく意味のある順番に並べると読みやすくなる。
例えば
- 対応するHTMLフォームのinputフィールドと同じ並び
- 重要度順
- アルファベット順
<br>


## 宣言をブロックにまとめる
様々な処理がずらっと並んでいると読む気が失せる。
そのため、適度に改行やコメントをすることで、各処理を論理的なグループにまとめて理解しやすくする。
<br>


## 参考
[リーダブルコード ―より良いコードを書くためのシンプルで実践的なテクニック 41~54P]https://www.amazon.co.jp/%E3%83%AA%E3%83%BC%E3%83%80%E3%83%96%E3%83%AB%E3%82%B3%E3%83%BC%E3%83%89-%E2%80%95%E3%82%88%E3%82%8A%E8%89%AF%E3%81%84%E3%82%B3%E3%83%BC%E3%83%89%E3%82%92%E6%9B%B8%E3%81%8F%E3%81%9F%E3%82%81%E3%81%AE%E3%82%B7%E3%83%B3%E3%83%97%E3%83%AB%E3%81%A7%E5%AE%9F%E8%B7%B5%E7%9A%84%E3%81%AA%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF-Theory-practice-Boswell/dp/4873115655
