#### js原型及原型链一些前置的知识点
> 构造函数: 一个普通函数用new关键字创建对象的时候,这个函数就叫做构造函数
> 每个对象都有__proto__和constructor属性,函数对象才有prototype属性,该属性指向一个对象

```
function Animal(name,age) {
  this.age = age
  this.name = name
  this.say = function() {
    return `我的名字是:${this.name}`
  }
}
Animal.prototype.color = 'white'

const cat = new Animal('miaomiao',3) // 此时Animal就叫做构造函数, cat就是Animal的实例, Animal.prototype就是原型对象
console.log( cat.__proto__ === Animal.prototype ) // true    __proto__ 属性指该对象的原型对象 
console.log( Animal.prototype.constructor === Animal ) // true    constructor属性指向与之关联的构造函数
```

```
console.log( cat.color ) // white cat本身没有color属性, 于是沿着__proto__属性向上查找,如果找到就返回，找不到则为undefined
cat.__proto__ === Animal.prototype // true
Animal.prototype.__proto__ === Object.prototype // true
Object.prototype.__proto__ === null // true
```
这条沿着__proto__向上查找的旅途就是原型链 

> Object.getPrototypeOf() 方法返回指定对象的原型
> hasOwnProperty() 返回布尔值,判断某个对象是否含有指定的属性,该方法会忽略掉那些从原型链上继承到的属性