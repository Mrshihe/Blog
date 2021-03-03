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
#### 自定义操作符new
new关键字执行过程(MDN)：
1.创建一个空的简单JavaScript对象（即{}）；
2.链接该对象（设置该对象的constructor）到另一个对象；
3.将步骤1新创建的对象作为this的上下文；
4.如果该函数没有返回对象，则返回this
```
function _new(fn,...args) { 
  const obj = Object.create(fn.prototype)
  const result = fn.apply(obj,args)
  return typeof result === 'object' ? result : obj
}
```
