# Javascriptのループ処理

## for文
```js
let hash = {
  key0: "value0",
  key1: "value1"
};

for (let key in hash) {
  alert('key:' + key + ' value:' + hash[key]);
}
```

## map
```js
{
  let hash = {
    key0: "value0", key1: "value1"
  };

  Object.keys(hash).map(key => console.log('key:' + key + ' value:' + hash[key]));
  // [undefined, undefined]が出てしまうが内容を確認したいだけなら十分
}
```

## forEach文
```js
{
  let hash = {
    key0: "value0", key1: "value1"
  };

  Object.keys(hash).forEach(key => console.log('key:' + key + ' value:' + hash[key]));
}
```
<br>

## 参考
[JavaScriptにおける連想配列のforループ操作 - Qiita]https://qiita.com/wifeofvillon/items/15359535a834832e08ea