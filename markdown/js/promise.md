```
function Promise(executor){
  const _this = this
  this.status = 'pending' // Promise 状态
  this.value = undefined // 成功结果
  this.reason = undefined // 失败原因
  this.onFulfilledCallbacks = [] // 存储成功的回调函数
  this.onRejectedCallbacks = [] // 存储失败的回调函数
  function resolve(value){
    if(_this.status === 'pending'){
      _this.status = 'fulfilled'
      _this.value = value
      // 兼容resolve异步执行的问题
      _this.onFulfilledCallbacks.forEach(fn => fn(_this.value))
    }
  }
  function reject(reason){
    if(_this.status==='pending'){
      _this.status = 'rejected'
      _this.reason = reason
      // 兼容reject异步执行的问题
      _this.onRejectedCallbacks.forEach(fn => fn())
    }
  }
  
  try{
    executor(resolve,reject)
  }catch{
    reject(e)
  } 
}

Promise.prototype.then = function(onFulfilled,onRejected){
  const _this = this
  let Promise2 = null
  // 处理onFulfilled 、onRejected 未传值的情况
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
  onRejected = typeof onRejected === 'function' ? onRejected : reason  => { throw reason  }

  promise2 = new Promise((resolve, reject) => {
    if(_this.status === 'pending'){
      _this.onFulfilledCallbacks.push((value) => {
        setTimeout(() => {
          try {
            let x = onFulfilled(value)
            resolvePromise(promise2, x, resolve, reject)
          } catch (reason) {
            reject(reason);
          }
        })
      });
      _this.onRejectedCallbacks.push((reason) => {
        setTimeout(() => {
          try {
            let x = onRejected(reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (reason) {
            reject(reason)
          }
        });
      });
    }
    if(_this.status === 'fulfilled'){
      setTimeout(() => {
        try {
          let x = onFulfilled(_this.value)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    }
    if(_this.status === 'rejected'){
      setTimeout(() => {
        try {
          let x = onRejected(_this.reason)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    }
  })
  // 返回一个新的Promise对象,实现then链式调用
  return Promise2;
}
  
// 根据promise/A+规范,对返回值进行解析
// 2.3.1. 如果promise和x引用同一个对象，用一个TypeError作为原因来拒绝promise
// 2.3.2. 如果x是一个promise，采用它的状态：[3.4]
//   2.3.2.1. 如果x是等待态，promise必须保持等待状态，直到x被解决或拒绝
//   2.3.2.2. 如果x是解决态，用相同的值解决promise
//   2.3.2.3. 如果x是拒绝态，用相同的原因拒绝promise
// 2.3.3. 否则，如果x是一个对象或函数
//   2.3.3.1. 让then成为x.then。[3.5]
//   2.3.3.2. 如果检索属性x.then导致抛出了一个异常e，用e作为原因拒绝promise
//   2.3.3.3. 如果then是一个函数，用x作为this调用它。then方法的参数为俩个回调函数，第一个参数叫做resolvePromise，第二个参数叫做rejectPromise：
//     2.3.3.3.1. 如果resolvePromise用一个值y调用，运行[[Resolve]](promise, y)。译者注：这里再次调用[[Resolve]](promise,y)，因为y可能还是promise
//     2.3.3.3.2. 如果rejectPromise用一个原因r调用，用r拒绝promise。译者注：这里如果r为promise的话，依旧会直接reject，拒绝的原因就是promise。并不会等到promise被解决或拒绝
//     2.3.3.3.3. 如果resolvePromise和rejectPromise都被调用，或者对同一个参数进行多次调用，那么第一次调用优先，以后的调用都会被忽略。译者注：这里主要针对thenable，promise的状态一旦更改就不会再改变。
//     2.3.3.3.4. 如果调用then抛出了一个异常e,
//       2.3.3.4.1. 如果resolvePromise或rejectPromise已经被调用，忽略它
//       2.3.3.4.2. 否则，用e作为原因拒绝promise
//   2.3.3.4. 如果then不是一个函数，用x解决promise
// 2.3.4. 如果x不是一个对象或函数，用x解决promise

// promise2:新的Promise对象  x:上一个then的返回值 resolve:promise2的resolve reject:promise2的reject
function resolvePromise(promise2, x, resolve, reject) {
  let called = false // called 防止多次调用
  // promise2 和 x为同一对象，则拒绝
  if(promise2 === x){
    return reject(new TypeError('loop is Error'))
  }
  //如果x不是null，是对象或者方法
  if(x!==null && ( typeof x === 'object' || typeof x === 'function')){
    try{
      // 防止使用Object.defineProperty()恶意抛错
      let then = x.then
      if (typeof then === 'function') {
        then.call(x, (y) => {
            // 别人的Promise的then方法可能设置了getter等，使用called防止多次调用then方法
            if (called) return
            called = true
            // 成功值y有可能还是promise或者是具有then方法等，再次resolvePromise，直到成功值为基本类型或者非thenable
            resolvePromise(promise2, y, resolve, reject);
        }, (reason) => {
            if (called) return
            called = true
            reject(reason)
        })
      } else {
        if (called) return
        called = true
        resolve(x)
      }
    }catch(reason){
      if (called) return
      called = true
      reject(reason)
    }
  }else{
    // x为普通值，直接resolve
    resolve(x)
  }
}
```