#### 优化高频率执行js代码的一种措施，如scroll\resize\mousemove

#### 防抖(事件被触发n秒后再执行回调,如果在这n秒内又被触发,则重新计时)

#### 简易版
```
function debounce(fn, delay) {
  let timer; 
  return function () {
    const _this = this; 
    const args = arguments;
    if (timer) {
        clearTimeout(timer);
    }
    timer = setTimeout(function () {
        fn.apply(_this, args); 
    }, delay);
  };
}
```
#### 应用场景
搜索框搜索输入\窗口大小Resize\验证输入手机号


#### 节流(单位时间内,只触发一次函数)

#### 简易版
```
function throttle(fn, delay) {
  let timer;
  return function () {
    const _this = this;
    const args = arguments;
    if (timer) return;
    timer = setTimeout(function () {
      fn.apply(_this, args);
      timer = null;
    }, delay)
  }
}
```
```
function throttle(fn, delay) {
  let previous = 0;
  return function() {
    const _this = this;
    const args = arguments;
    const now = new Date();
    if(now - previous > delay) {
      fn.apply(_this, args);
      previous = now;
    }
  }
}
```
#### 应用场景
搜索联想功能\监听滚动