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

export const getDeviceType: () => 'android'| 'ios' | 'pc'  = getSingleDesgin(_getDeviceType)

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
     * encodeURIComponent(value) 可能某些key带有&符号 兼容该行为
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

## 深拼合数组

```ts
/**
 * 深拼合数组
 * @param {*} arr 
 * @returns deepFlatten([1,[2],[[3],4],5]) -> [1,2,3,4,5]
 */
 
export const deepFlatten = arr => [].concat(...arr.map(v => Array.isArray(v) ? deepFlatten(v) : v));
```


## 检查对象内是否缺少某个字段
```ts
/**
 * 检查对象内是否缺少某个字段 并返回缺少的字段
 * @param needCheckParams 
 * @param isReqParams 
 * @returns string[]
 */
export const checkParamsMiss = function(needCheckParams: object, isReqParams: string[]): string[] {
    // 非对象直接返回参数校验
    if (isEmptyObject(needCheckParams)) return isReqParams

    return isReqParams.filter(key => {
        const v = needCheckParams[key]
        // 该值为非0假值
        if (v !== 0 && !v) {
            return key
        }
    })
}
```

## 检验参数装饰器
> 装饰器方式使用 依赖checkParamsMiss
```ts
/**
 * 检验参数装饰器
 * @checkParamsMissDec(['a', 'b'], (isReqParams) => isReqParams)
 *
 * @param isReqParams  必填的参数列表
 * @param callback 当发生错误时 不会执行原函数 会执行callback
 * 
 */
export const checkParamsMissDec = function (isReqParams: string[], callback?: Function) {

    return function (target, name, descriptor) {
        const oldValue = descriptor.value;
        descriptor.value = function(...arg: any[]) {
            const needCheckParams = arg[0]

            console.log(`检验 ${name} 参数是否传递正确`, needCheckParams, isReqParams)

            const p = checkParamsMiss(needCheckParams, isReqParams)

            if (p.length) {
                console.error(` ${name} 缺少必填参数`, ...p)
                return callback && callback(p)
            }
            // 参数检验没问题执行原函数
            return oldValue.apply(this, arg);
        }

        return descriptor;
    }
}
```


## 将对象字段修正为另外的key

```ts
/**
 * 将数据字段修正
 * @param toFixParams         想要修正的参数
 * @param fixFieldParams      修正成什么字段
 * @param config        
 *          isWarn      是否需要预警字段被修正 因为该方法会删除传入的字段
 *          warnText    
 * @returns fixField({a:1}, {a:'b'}) => {b:1}
 */
export const fixField = function (toFixParams: object, fixFieldParams: object, config?: fixFieldConfigRes  ) {
    if (isEmptyObject(toFixParams)) return {}
    const { isWarn = false, warnTextFunc = warnTextFuncDef } = config || {}
    let newObject = {...toFixParams}
    for (const [key, value] of Object.entries(toFixParams)) {
        let newKey = fixFieldParams[key]
        if (newKey) {
            // 只是输出提示
            isWarn && console.warn(warnTextFunc(key, newKey))
            newObject[newKey] = value
            delete newObject[key]
        }
    }
    return newObject
}

function warnTextFuncDef(key, newKey) {
    return `字段${key}被重置为${newKey}`
}
interface fixFieldConfigRes {
    isWarn?: boolean;
    warnTextFunc?: (key: string, newKey: string) => string
}

```

## 将对象stringify
```ts
/**
 * 将对象stringify
 */
export const toStringify = function(obj) {
    if (isObject(obj)) return JSON.stringify(obj)
    return obj
}
```