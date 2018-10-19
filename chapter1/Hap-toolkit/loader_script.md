### 解析script



```javascript
var $app_script$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/script-loader.js!../../node_modules/babel-loader?presets[]=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&presets=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&plugins[]=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&plugins=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&comments=false!../../node_modules/hap-toolkit/tools/packager/webpack/loader/access-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=script!./index.ux?uxType=page")
```



> 解析script部分使用到的loader

- 1、Fragment-loader.js?index=0&type=script!
- 2、Access-loader.js
- 3、Jsx-loaer.js
- 4、Babel-loader
- 5、Script-loader



####  1、Fragement-loader.js

```javascript
parseFragmentsWithCache(source, resourcePath)[type] // 获取到 frags（前面提及）缓存代码的 type的内容
```



#### 2、Access-loader.js

```javascript
添加 const accessors = ['public', 'protected', 'private'] 三种数据对象
```



#### 3、jsx-loader.js

 	处理jsx内容



#### 4、Babel-loader.js

将es6转成es5



#### 5、Script-loader

```javascript
// 将 import 或则 require中引入@sytem.** 或者@service.** 内部模块转换成@app-module/system.**

import router from '@system.router'

// var _system = $app_require$('@app-module/system.router');
```