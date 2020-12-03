# 別nameのラジオボタンを紐付け

## やりたいこと
name属性の値は別で、ラジオボタンを1つだけ選択できるようにしたい。

## 実装
紐付けしたいラジオボタンのクラスを同じに設定し、jQueryにて以下機能を実装
```html
<input type="radio" name="input[1]['radio']" class="radio" checked>
<input type="radio" name="input[2]['radio']" class="radio" >
```
```js
$(".radio").on("click", function(){
    $('.radio').prop('checked', false);  //  全部のチェックを外す
    $(this).prop('checked', true);  //  押したやつだけチェックつける
});
```
radioクラスの要素をクリックしたら、1度全てのradioクラスのcheckedを外し、その後選択したradio要素にcheckedを付ける。

## 別の書き方
以下の書き方でも良い。

radioクラス要素の状態が変化した際にイベント発生。
radioクラスのうち、選択された要素以外のradioクラスのcheckedを外す。
```js
$('input[type="radio"]').on('change', function() {
   $('input[type="radio"]').not(this).prop('checked', false);
});
```

## 参考
[jQueryで複数選択不可のチェックボックスをつくる - Qiita]https://qiita.com/shosho/items/05168b2cb5b868536d61
[html — htmlグループ内の1つのチェックボックスのみを選択]https://www.it-swarm-ja.tech/ja/html/html%E3%82%B0%E3%83%AB%E3%83%BC%E3%83%97%E5%86%85%E3%81%AE1%E3%81%A4%E3%81%AE%E3%83%81%E3%82%A7%E3%83%83%E3%82%AF%E3%83%9C%E3%83%83%E3%82%AF%E3%82%B9%E3%81%AE%E3%81%BF%E3%82%92%E9%81%B8%E6%8A%9E/941788693/
