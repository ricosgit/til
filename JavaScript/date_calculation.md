# 日付オブジェクトを使用して日付の計算をする
<br>

## 日付オブジェクトの生成
付オブジェクトの生成fは大まかに分けて下記の通り。
```js
// 引数を指定せず生成（現在時刻）
var now = new Date();
console.log(now); // Mon Dec 21 2020 20:38:37 GMT+0900 (日本標準時)

// 年、月、日を指定して生成（月は「1月→0」、「2月→1」となるので、実際の月から-1する必要がある））
var now = new Date(1994, 10 - 1, 17); // 1994-10-17 00:00:00.000

// 1970-01-01 00:00:00(UTC)からの秒数(ミリ秒単位)で指定
var now = new Date(946652399000);

// 文字列で指定
var now = new Date("Fri, Dec 03, 1999 23:59:59 +0900");  // RFC 2822
var now = new Date("Dec 3, 1999 23:59");                 // RFC 2822
var now = new Date("1999-12-31T23:59:59+0900");          // ISO 8601
var now = new Date("1999-12-31T23:59:59");               // ISO 8601
var now = new Date("1999/12/31 23:59:59");
var now = new Date("12/31/1999 23:59:59");
var now = new Date("99/12/31 23:59:59");
var now = new Date("12/31/99 23:59:59");
// 
```
<br>

## 日付オブジェクトの加算減算
指定した日付から〜日後（前）の日付を指定したい、
などの場合は以下のようにして計算できる。
```js
var date = new Date(2020, 4 - 1, 1); // 2020-04-01
date.setDate(date.getDate() - 1); // 1日前の日付をセット
console.log(date); // 2020-03-31
```
月単位で計算したい場合は**setMonth()**、**getMonth()** を使用すればok

※ただし、上記の方法だと元の値が上書きされてしまうので、
元のあたいとは別に1日前の日付オブジェクトを取得したいなどの場合は以下のようにする。
```js
var date = new Date(2020, 4 - 1, 1);
var aDayAgo = new Date(date.getTime()); // 上と同じ日付でコピー
aDayAgo.setDate(aDayAgo.getDate() - 1);
console.log(date); // 2020-04-01
console.log(aDayAgo); // 2020-03-31
```
<br>

## 日付オブジェクトの比較
2つの日付オブジェクトの日時を比較したい場合は以下のようにする。
```js
// 年、月、日が同じかチェック
function isDateEqual(checkDate, today) {
  var month1 = checkDate.getMonth() + 1;
  var day1 = checkDate.getDate();

  var month2= today.getMonth() + 1;
  var day2 = today.getDate();

  if (month1 == month2) {
    return day1 == day2;
  } else {
    return false;
  }
}

// ミリ秒単位で日時が同じかチェック
function isDateEqual(checkDate, today) {
  return date1.getTime() == date2.getTime();
}
```
<br>

## 参考
[日付（Date） - とほほのWWW入門]http://www.tohoho-web.com/js/date.htm
[【JavaScript入門】日付の比較とチェックをする方法 | 侍エンジニア塾ブログ（Samurai Blog） - プログラミング入門者向けサイト]https://www.sejuku.net/blog/23115