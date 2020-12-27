# javascriptの判定

## 各型、値の判定
|value|type|result|
|--|--|--|
|{}|オブジェクト|true|
|"hoge"|文字列|true|
|""|文字|false|
|1|数値|true|
|-1|数値|true|
|0|数値|false|
|[]|配列|true|
|true|真偽値|true|
|false|真偽値|false|
|undefined|undefined|false|
|null|null|false|

## undefinedとnullの違い
```js
var var1;
console.log(var1);  // undefined (メモリだけ確保したが、初期化されていない)
var1 = null;
console.log(var1);  // null (初期化されているが、型も値も決まっていない)
```

## 参考
[[JavaScript] null とか undefined とか 0 とか 空文字('') とか false とかの判定について - Qiita]https://qiita.com/phi/items/723aa59851b0716a87e3#undefined-%E3%81%A8%E3%81%84%E3%81%86%E5%95%8F%E9%A1%8C%E5%85%90

[(初心者向け) JavaScript の null と undefined - Qiita]https://qiita.com/tadnakam/items/ffaef84ee89fe659ed15
