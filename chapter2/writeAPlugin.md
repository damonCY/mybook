##  webpack 使用描述

配置介绍

```javascript
module.exports = {
        entry: {
            collection: 'main.js'     // collection为chunk的名字，chunk的入口文件是main.js
        }
        output: {
            path: __dirname + "/build",
            filename: "bundle-[hash].js"
        },
        devtool: 'source-map',
        devServer: {},
        module: {
            rules: [{
            test: /(\.jsx|\.js)$/,
            use: { loader: loader: 'babel-loader?fakeoption=true!eslint-loader'},
            exclude: /node_module/,  // 匹配的文件进行过滤，排除node_module目录下的文件
            include: './src'  // 指定匹配文件的范围
            }]
    },
    plugins: [new webpack.BannerPlugin('版权所有，翻版必究')]
};
```



**loader属性配置**: **值为字符串**，对于匹配的文件使用babel-loader和eslint-loader处理，处理顺序**从右向左**，先eslint-loader，后babel-loader，**loader之间用**`！`隔开, loader与options用`？`隔开**



**Chunk 概念： chunk表示一个文件**，默认情况下webpack的输入是一个入口文件，输出也是一个文件，这个文件就是一个chunk，chunkId就是产出时给每个文件一个唯一标识id，chunkhash就是文件内容的md5值，name就是在entry中指定的key值。

