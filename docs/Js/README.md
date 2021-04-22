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

## 单例模式

```ts
 /**
 * 单例模式
 * @description 将函数包装为单例方法
 * @param {*} fn 
 * 
 */
export function getSingleDesgin(fn: Function): any {
    /**
     * 返回值
     */
    let result: any;
    return function (...args: any[]) {
        /**
         * 如果有就直接返回  否则就执行回调
         */
        return result || (result = fn.apply(this, args))
    }
}
```

## 获取设备类型

```ts
/**
 * 获取设备类型
 * @returns {string}
 */
function _getDeviceType(): 'android'| 'ios' | 'pc' {
    let u = window.navigator.userAgent;
    let isAndroid = u.indexOf('Android') > -1;
    let isIos = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
    if (isAndroid) return 'android'
    if (isIos) return 'ios'
    return 'pc';
}

export const getDeviceType: () => 'android'| 'ios' | 'pc'  = 
getSingleDesgin(_getDeviceType)

```

## 把对象转化成字符串拼接

```ts
/**
 * 把对象转化成字符串拼接
 * @param {*} data
 * @param joinStr
 * @returns {a:1,b1} => a=1&b=1
 */
interface _stringifyConfigType {
    /**
     * 是否将值加码 用于值包含特殊符合时
     * encodeURIComponent(value) 目前为了兼容活动传递的 userid可能带有&符号
     */
    isEncodeParam?: boolean | false;
    /**
     * 以什么拼合
     */
    joinStr?: string | '&';

}
export const _stringify = function (data: any, config?: _stringifyConfigType): string {
    // 非对象或者空对象都认为是空对象
    if (isEmptyObject(data)) {
        return isString(data) ? data : ''
    }
    const { isEncodeParam = false, joinStr = '&' } = config || {}
    let _result = [];
    for (let key in data) {
        if (data.hasOwnProperty(key)) {
            let value = isEncodeParam ? encodeURIComponent(data[key]) : data[key];
            _result.push(key + '=' + value);
        }
    }
    return _result.join(joinStr);
}

```