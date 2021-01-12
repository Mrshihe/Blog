### 批量导入全局组件
#### 1. 新增components/index.js文件
```
export default function registerComponent (Vue) {
  /**
   * 参数说明：
   * 1. 其组件目录的相对路径
   * 2. 是否查询其子目录
   * 3. 匹配基础组件文件名的正则表达式
   **/
  const modules = require.context('./', false, /\w+.vue$/)
  modules.keys().forEach(fileName => {
    // 获取组件配置
    const component = modules(fileName)
    // 获取组件名称，去除文件名开头的 `./` 和结尾的扩展名，文件名避免与html标签重复 如header/footer
    const name = fileName.replace(/^\.\/(.*)\.\w+$/, '$1')
    // 注册全局组件
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    Vue.component(name, component.default || component)
  })
}
```
#### 2. main.js中导入注册模块进行注册
```
import registerComponent from './components'
registerComponent(Vue)
```

### 动态改变某个页面是否缓存
- 假设有A, B, C三个页面, 需求是A到B, B无需缓存, B到C,从C返回B的时候B需要缓存
#### 1. 使用keep-alive的include属性, 将keepAlive存入store
```
<keep-alive :include='keepAlive'>
  <router-view></router-view>
</keep-alive>

computed: {
  keepAlive () {
    return this.$store.getters.keepAlive
  }
}
```
#### 2. store内设置keepAlive及动态添加、删除方法
```
const state = {
  keekAlives: []
}
const mutations = {
  SET_KEEP_ALIVE(state, keekAlivesName){
    if(state.keekAlives.indexOf(keekAlivesName)==-1){
      state.keekAlives.push(keekAlivesName)
    }
  },
  DEL_KEEP_ALIVE(state, keekAlivesName){
    let index = state.keekAlives.indexOf(keekAlivesName)
    if(index>-1){
      state.keekAlives.splice(index,1)
    }
  }
}
```
#### 3. 监听路由变化 根据实际情况设置是否缓存（B组件内）
```
beforeRouteLeave (to, from, next) {
  if (to.path !== 'C') { 
    this.$store.commit('DEL_KEEP_ALIVE', 'B') 
  }
  next()
}
```
#### 4. 全局路由导航钩子
```
router.beforeEach((to, from, next) => {
  if(to.path === 'B'){
    store.commit('SET_KEEP_ALIVE','B')
  }
}
```