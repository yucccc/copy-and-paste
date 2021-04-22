# 常用Js片段

## 回到顶部

平滑滚动到顶部


```js
const scrollToTop = () => {
  const c = document.documentElement.scrollTop || document.body.scrollTop;
  if (c > 0) {
    window.requestAnimationFrame(scrollToTop);
    window.scrollTo(0, c - c / 8);
  }
};
scrollToTop()
```

## 获取 url 上的参数

```js
const getURLParameters = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => (
      (a[v.slice(0, v.indexOf("="))] = v.slice(v.indexOf("=") + 1)), a
    ),
    {}
);

getURLParameters("http://url.com/page?name=Adam&surname=Smith"); 
// {name: 'Adam', surname: 'Smith'}
getURLParameters("google.com"); 
// {}

```

## 是否属于某个类型


```js
// 是否数字
export function isNumber (v) {
  return typeof v === 'number'
}
// 是否字符串
export function isString (v) {
  return typeof v === 'string'
}
// 是否对象
export function isObject (v) {
  return Object.prototype.toString.call(v) === '[object Object]'
}
// 是否函数
export function isFunction (v) {
  return typeof v === 'function'
}
// 是否undefined
export function isUndefined (v) {
  return v === undefined
}
// 布尔
export function isBoolean (v) {
  return typeof v === 'boolean'
}
// 数组
export function isArray (v) {
  return Array.isArray(v)
}
// 空对象
export function isEmptyObject (v) {
  if (!isObject(v)) return true
  return Object.keys(v).length === 0
}
```