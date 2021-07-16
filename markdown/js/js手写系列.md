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
// ES5 由于无法通过解构的形式传入参数，通过字符串拼接然后再通过eval来执行或者new Function()
Function.prototype.mycall = function(context){
  context = context || window
  var args = []
  for(var i=0; i<arguments.length; i++){
    args.push('arguments[' + i + ']')
  }
  var argstr = args.join(',')
  context.fn = this
  var res =  eval('context.fn(' + argstr + ')')
  delete context.fn
  return res
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
2.将空对象的原型赋值为构造器函数的原型；
3.将步骤1新创建的对象作为this的上下文；
4.如果该函数没有返回对象，则返回新创建的对象；
```
function _new(fn,...args) { 
  const obj = Object.create(fn.prototype)
  const result = fn.apply(obj,args)
  return typeof result === 'object' ? result : obj
}
```
#### 自定义instanceof
语法：(object) instanceof (constructor)
检测constructor.prototype是否存在于参数object的原型链上
```
function myInstanceof(left,right) {
  let L = left.__proto__, RP = right.prototype
  while(true){
    if( L === null ){
      return false
    }
    if( L === RP ){
      return true
    }
    L = L.__proto__
  }
}
```