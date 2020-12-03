# 動的に追加した要素にjQueryイベントを発火させる方法


## 発生した問題
動的に追加した要素に大してjQueryのonclickイベントを発火させようと思ったが、ダブルクリックしないと発火しない。


## 原因
動的に要素を追加すると、イベントハンドラが未登録になっているから。


例えば以下のhtmlとjQueryを実装した場合、
```html
<div id="btns_box">
  <button class="clickme_btn">Click Me</button>
</div>

<br>
<button id="add_clickme_btn_btn">Add Button</button>
```
```js
$(function(){
  /** 追加されるボタンにイベントを追加 */
  $('.clickme_btn').on('click', function(){
    alert('Button is clicked');
  });
  
  /** ボタンの追加処理 */
  $('#add_clickme_btn_btn').on('click', function(){
    $('#btns_box').append(
      '<button class="clickme_btn">Click Me</button>'
    );
  });
});
```
ボタンの追加処理については問題ないが、追加したボタンを押した場合にはアラートが出ない。
なぜかと言うと、上記のコード
```js
$('.clickme_btn').on('click', function(){
    alert('Button is clicked');
});
```
これが期待通りに動くのは**イベント登録した要素が既にDOM上に存在する場合**のみ。


## 解決策
**document に対してイベントを登録する。**
`$(document).on`のようにウェブページ全体に対してイベントハンドラを登録し、`on('click', '.clickme_btn' ...`でイベント処理する要素を絞り込む。


以下のコードで解決する。
```js
$(document).on('click', '.clickme_btn', function(){
  alert('Button is clicked');
});
```


## 参考
[jQueryでイベントが発火しないときの簡単な対処法 | PisukeCode - Web開発まとめ]https://pisuke-code.com/jquery-solution-for-event-unfired/