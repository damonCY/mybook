## webpack配置概述



### [webpack.config.js]()

```javascript
module.exports = {
  // 入口文件，是模块构建的起点，同时每一个入口文件对应最后生成的一个 chunk。
  entry: {
    main: './main.js'
  },
  // 文件路径指向(可加快打包过程)。
  resolve: {
    alias: {
      'react': pathToReact
    }
  },
  // 生成文件，是模块构建的终点，包括输出文件与输出路径。
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: '[name].js'
  },
  // 这里配置了处理各模块的 loader ，包括 css 预处理 loader ，es6 编译 loader，图片处理 loader。
  module: {
    rules: [{
        test: /(\.jsx|\.js)$/,
        use: { loader: loader: 'babel-loader?fakeoption=true!eslint-loader'},
        exclude: /node_module/,  // 匹配的文件进行过滤，排除node_module目录下的文件
        include: './src'  // 指定匹配文件的范围
    }]
  }, 
    // 统计信息
   stats: 'errors-only', 
  // webpack 各插件对象，在 webpack 的事件流中执行对应的方法。
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ]
};
```



**loader属性配置**: **值为字符串**，对于匹配的文件使用babel-loader和eslint-loader处理，处理顺序**从右向左**，先eslint-loader，后babel-loader，**loader之间用**`！`隔开, loader与options用`？`隔开**



**Chunk 概念： chunk表示一个文件**，默认情况下webpack的输入是一个入口文件，输出也是一个文件，这个文件就是一个chunk，name为在entry中指定的key值。

### 常用属性

* [stats](https://webpack.docschina.org/configuration/stats)
