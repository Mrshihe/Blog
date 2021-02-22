### prerender-spa-plugin预渲染插件，用于构建少量得营销页面 实现SEO
### 该demo版本，vue 2.5.2 / prerender-spa-plugin 3.4.0

### 安装prerender-spa-plugin
`npm install prerender-spa-plugin`

## 配置相关文件
### webpack.prod.conf.js
```
// SPA预渲染
const PrerenderSPAPlugin = require('prerender-spa-plugin')
const Renderer = PrerenderSPAPlugin.PuppeteerRenderer

plugins:[
  .............
  new PrerenderSPAPlugin({
    // 这个目录只能有一级，如果目录层次大于一级，在生成的时候不会有任何错误提示，在预渲染的时候只会卡死
    staticDir: path.join(__dirname, '../dist'),
    // 对应自己的路由文件，（不建议动态路由）
    routes: ['/', '/abs', '/information', '/redpackets', '/produce' ],
    renderer: new Renderer({
      inject: {
        foo: 'bar'
      },
      headless: false,
      // 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
      renderAfterDocumentEvent: 'render-event'
    })
  })
]
```
### main.js
```
new Vue({
  ...........
  mounted () {
    document.dispatchEvent(new Event('render-event'))
  }
})
```

# 一定要注意配置文件得位置！！！ (如打包文件名为 website)
###### webpack.base.conf.js 中 publicPath: '/website/'
###### webpack.prod.conf.js 中 output{publicPath: '/'}
###### config=>index.js 中 assetsPublicPath: '/website/'
###### config=>index.js 中 assetsSubDirectory: 'website/static'
###### config=>index.js 中 assetsRoot: path.resolve(__dirname, "../dist")