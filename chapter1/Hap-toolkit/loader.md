### loader



#### 1、ux文件的loader入口 Ux-loader.js



* 解析ux文件

```javascript
// 片段缓存
const fragsCache = new Map()
parseFragmentsWithCache (source, filePath) {
  if (!fragsCache.has(filePath) || fragsCache.get(filePath).source !== source) {
    // 手动的缓存文件，提高性能
    fragsCache.set(filePath, {
      source: source,
      frags: parseFragments(source)
    })
  }
  return fragsCache.get(filePath).frags // 返回缓存文
}
// frage 
const frags = {
    import: [],  // 导入组件
    template: [],  // 模板
    style: [],  // 样式
    script: []  // 脚本
  }
```



* parseFragements (source) [pase5](https://github.com/inikulin/parse5/blob/master/packages/parse5/docs/index.md#parsefragment)

```javascript
//使用parse5拆分source文件为四部分
const fragment = parse5.parseFragment(source, {
    treeAdapter: parse5.treeAdapters.default,
    locationInfo: true
  })
// 查看 fragement数据
{ nodeName: '#document-fragment',
  childNodes: [ { nodeName: 'template', 
       tagName: 'template',
       attrs: [],
       namespaceURI: 'http://www.w3.org/1999/xhtml',
       childNodes: [],
       parentNode: [Circular],
       content: [Object], 
       __location: [Object] },
     { nodeName: '#text',
       value: '\n\n',
       parentNode: [Circular],
       __location: [Object] },
     { nodeName: 'style',
       tagName: 'style',
       attrs: [],
       namespaceURI: 'http://www.w3.org/1999/xhtml',
       childNodes: [Array],
       parentNode: [Circular],
       __location: [Object] },
     //  使用parse5 把ux文件代码JSON化，拆分成四部分,分别放置到frags中待后续处理
```



* parseImportList($loader, frags.import)

```javascript
// 处理frags.import组件
return parseImportList($loader, frags.import)
// 给每个import对象添加两个属性
importItem.isValid = true
importItem.srcPath = result
```



##### assemble($loader, frags, name, uxType, resourcePath)

> uxType=== 'app'的情况

对于app.ux字处理script中的内容

所以： app.ux 不能设置template和style相关的内容

对ux文件的内容进行模块化的包装。后面会删除webpack自带的	`__require__module` 方式

```javascript
// 处理脚本
output += processScriptFrag($loader, frags.script, uxType)

output += `\n$app_define$('@app-application/${name}', [], function($app_require$, $app_exports$, $app_module$){\n`
if (frags.script.length > 0) {
    output += `     $app_script$($app_module$, $app_exports$, $app_require$)\n`
    output += `     if ($app_exports$.__esModule && $app_exports$.default) {
    $app_module$.exports = $app_exports$.default
}\n`
}
output += '})\n'

const pkg = JSON.parse(fs.readFileSync(packagepath).toString())
output += `\n$app_bootstrap$('@app-application/${name}',{ packagerVersion: '${pkg.subversion.packager}'})\n`

```





```javascript
=======output---- var $app_template$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/json-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/template-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=template!./index.ux?uxType=page")
var $app_style$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/json-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/style-loader.js?index=0&type=style!../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=style!./index.ux?uxType=page")
var $app_script$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/script-loader.js!../../node_modules/babel-loader?presets[]=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&presets=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&plugins[]=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&plugins=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&comments=false!../../node_modules/hap-toolkit/tools/packager/webpack/loader/access-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=script!./index.ux?uxType=page")

$app_define$('@app-component/index', [], function($app_require$, $app_exports$, $app_module$){
     $app_script$($app_module$, $app_exports$, $app_require$)
     if ($app_exports$.__esModule && $app_exports$.default) {
            $app_module$.exports = $app_exports$.default
        }
     $app_module$.exports.template = $app_template$
     $app_module$.exports.style = $app_style$
})

$app_bootstrap$('@app-component/index',{ packagerVersion: '0.0.5'})
```



> 分析： processScriptFrag($loader, frags.script, uxType)

```
retStr = 'var $app_script$ = ' +
      makeRequireString(
        $loader,
        makeLoaderString(FRAG_TYPE.SCRIPT, {
          alone: !!fragAttrsSrc,
          path: $loader.resourcePath
        }, uxType),
        `${src}?uxType=${uxType}`
      )
//  构造script模块需要使用到的loader集。”！！“代表执行loader,使用‘！’分隔loader `?`分隔option
var $app_script$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/script-loader.js!
../../node_modules/babel-loader?presets[]=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&presets=/Users/chenyong/Work/project/demo/node_modules/babel-preset-env&plugins[]=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&plugins=/Users/chenyong/Work/project/demo/node_modules/hap-toolkit/tools/packager/webpack/loader/jsx-loader.js&comments=false!
../../node_modules/hap-toolkit/tools/packager/webpack/loader/access-loader.js!
../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=script!
./index.ux?uxType=page")
// webpack从右到左执行loader
```

> 解析script部分使用到的loader

* 1、Fragment-loader.js?index=0&type=script!
* 2、Access-loader.js
* 3、Jsx-loaer.js
* 4、Babel-loader
* 5、Script-loader

> 1、Fragement-loader.js

```
parseFragmentsWithCache(source, resourcePath)[type] // 获取到 frags（前面提及）缓存代码的 type的内容
```

> 2、Access-loader.js

```
添加 const accessors = ['public', 'protected', 'private'] 三种数据对象
```

> 3、jsx-loader.js

处理jsx内容

> 4、Babel-loader.js

将es6转成es5

> Script-loader

```
// 将 import 或则 require中引入@sytem.** 或者@service.** 内部模块转换成@app-module/system.**
import router from '@system.router'
// var _system = $app_require$('@app-module/system.router');
​```chapter4
```

