# Javascriptの分岐処理

## if文
基本はphpと同じ。
ただ、jsには**elseif**は存在しないため、**else if**を使用する。
```js
if (条件式) {
    // 処理
} else if (条件式) {
    // 処理
} else {
    // 処理
}
```
<br>

## switch文
switch文に関してはphpと全く同じ。
```js
switch (expression) {
  case value1:
    // 式の結果が value1 に一致する場合に実行する文
    [break;]
  case value2:
    // 式の結果が value2 に一致する場合に実行する文
    [break;]
  ...
  case valueN:
    // 式の結果が valueN に一致する場合に実行する文
    [break;]
  [default:
    // 式の値に一致するものが存在しない場合に実行する文
    [break;]]
}
```

## 参考
[switch - JavaScript | MDN]https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Statements/switch
