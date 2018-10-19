### webpack 使用





#### 1、通过Node.js API 启动 webpack



*安装*

```
npm i -D webpack
```

*使用*

```
const webpack = require('webpack')
webpack({
// webpack配置，和webpack.config.js一致
}，(err, stats)=> {
   if(err || stats.hasErrors()){ // 构建过程出错} 
   // 成功完成构建
})
// 或者
const config = requir('./webpack.config.js')
webpack(config, callback)
```

