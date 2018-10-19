##  Webpackplugin 插件编写



**常用结构：**

```javascript
class BasicPlugin {
    // 在构造函数中用户为该插件传入的配置
    constructor(options) {
    }
        apply (compiler) {
            compiler.plugin('compilation', function(compilation) {
            })
        }    
}
module.exports = BasicPlugn;
```



**使用plugin时，相关配置代码**

```javascript
const BasicPlugin = require('./BasicPlugin.js')
module.exports = {
    plugins: [
        new BasicPlugin(optins)
    ]
}
```

webpack启动后，读取配置的过程中会执行new BasicPlugin(options)，初始化一个BasicPlugin并获得示例。在初始化compiler对象后，在调用basicPlugin.apply(compiler)为插件实例传入compiler对象。插件实例获取到compiler对象后就可以通过compiler.plugin(事件名，回调函数)监听到webpack广播事件。

**compiler对象**：包含webpack环境的所有配置信息，包含options、loaders、plugins等信息。可以理解为webpack的实例

**compilation对象**：包含当前模块资源、编译生成资源、变化的文件等。当webpack以开发模式运行时，每当检查到一个文件发生变化，便有一次新的Compilation被创建。compilation提供了大量的事件回调供插件扩展。

**compiler与compilation区别**：compiler代表整个webpack从启动到关闭的生命周期，而compilation只代表一次新的编译

​	



### plugin：webpack 的插件实体，这里以 UglifyJsPlugin 为例。

```javascript
function UglifyJsPlugin(options) {
  this.options = options;
}

module.exports = UglifyJsPlugin;

UglifyJsPlugin.prototype.apply = function(compiler) {
  compiler.plugin("compilation", function(compilation, callback) {
    compilation.plugin("build-module", function(module) {
    });
    compilation.plugin("optimize-chunk-assets", function(chunks, callback) {
      // Uglify 逻辑
    });
    compilation.plugin("normal-module-loader", function(context) {
    });
      // 对于异步事件，需要调用callback来通知webpack本次事件监听处理结束
      // 如果忘记调用callback，则webpack将一直卡在这里无法往下执行
      callback()
  });
};
```



### [compiler事件钩子](https://webpack.docschina.org/api/compiler-hooks/)

![compiler](../source/WechatIMG52.png)

