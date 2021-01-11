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