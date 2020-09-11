```
let a = { n:1 }
let b = a
a.x = a = { n:2 }
console.log(a.x) // undefined
console.log(b) // { n:1, x:{n:2} }
```
### 解析
前两步 a,b为同一对象,假定为001; 第三步的时候由于 . 的运算符优先级高于 = , 所以先访问a.x 因为不存在则赋值为undefined , 然后 a = { n:2 }, a变成另一个对象,假定002; 同是将{n:2}赋值给001对象的x console.log( a.x ) 此时访问的是002的x,值也为undefined; console.log(b) 访问的是001，则为 {n:1,x:{n:2}} 