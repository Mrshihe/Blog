#### 自定义call
```
Function.prototype.myCall = function (context,...params){
  let key = Symbol('key'), // 唯一值避免冲突
            result;
  context = Object(context) || window // context为传入参数
  context[key] = this // 调用的函数
  result = context[key](...params)
  delete context[key]
  return result
}
```
#### 自定义apply
```
Function.prototype.myApply = function(context,params){
  let key = Symbol('key'),result;
  context = Object(context) || window
  context[key] = this
  result = context[key](...params)
  delete context[key]
  return result
}
```
#### 自定义bind
```
Function.prototype.myBind = function(context,...params){
  const fn = this
  return function(...args){
    return fn.apply(context,[...params,...args])
  }
}
```