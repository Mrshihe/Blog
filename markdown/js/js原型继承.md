### JS6种继承方式

#### 父类
```
function Animal(name) {
  this.name = name
  this.say = function (sound) {
    return `我是${this.name},我的叫声是${sound}`
  }
}
Animal.prototype.animalType = function(type) {
  return `我是${type}动物`
}
```

### 1.原型链继承
```
function Cat(color) {
  this.color = color
}
Cat.prototype = new Animal() // 父类的实例作为子类的原型
const cat1 = new Cat('bule')
```
cat1即是Cat实例，也是Animal实例

#### 特点
1. 父类的原型方法/属性，子类都能访问
#### 缺点
1. 不能向父类构造函数中传参数
2. 父类中的引用类型会被所有子类实例共享，一个子类改变则所有子类改变,无法实现多继承
3. 新增原型属性和方法必须在 Cat.prototype = new Animal() 之后


### 2.构造继承
```
function Cat(name,color) {
  Animal.call(this,name)
  this.color = color
}
const cat1 = new Cat('小猫','blue')
```
cat1只是Cat实例，不是Animal实例

#### 特点
1. 创建子类实例时可以向父类传递参数
2. 解决了父类引用属性共享的问题，可以实现多继承
#### 缺点
1. 不能继承父类原型的属性和方法
2. 方法都在父类构造函数中定义，无法复用


### 3.组合继承(构造函数+原型链)
```
function Cat(color) {
  Animal.call(this)
  this.color = color
}
Cat.prototype = new Animal()
Cat.prototype.constructor = Cat // 修正constructor指向
const cat1 = new Cat('小猫','blue')
```
cat1即是Cat实例，也是Animal实例

#### 特点
1. 可以继承实例属性/方法和原型属性/方法
2. 父类函数可复用，可以多继承，可以传参
#### 缺点
1. 调用两次父类构造函数，生成了两份实例，冗余(子类实例和子类原型实例都有name属性)


### 4.寄生组合式继承
```
function Cat(name,color) {
  Animal.call(this,name)
  this.color = color
}

myPrototype(Cat,Animal)

function myPrototype(child,parent) {
  const prototype = object(parent.prototype)
  prototype.constructor = child
  child.prototype = prototype
}
// 复制了一份父类的原型对象, 避免修改子类原型对象时也修改了父类的原型对象
function object(obj) {
  function F() {}
  F.prototype = obj;
  return new F();
}
const cat1 = new Cat('小猫','blue')
```
#### 特点
1. 解决了调用两次父类构造函数的问题