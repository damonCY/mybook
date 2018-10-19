### loader: style模块解析

```javascript
var $app_style$ = require("!!../../node_modules/hap-toolkit/tools/packager/webpack/loader/json-loader.js!../../node_modules/hap-toolkit/tools/packager/webpack/loader/style-loader.js?index=0&type=style!../../node_modules/hap-toolkit/tools/packager/webpack/loader/fragment-loader.js?index=0&type=style!./index.ux?uxType=page")
```



#### 解析style部分使用到的loader

* 1、Fragment-loader.js?index=0&type=template!
* 2、style-loader.js?index=0&type=style!
* 3、json-loader.js!



#### 1、Fragment-loader.js?index=0&type=template!

```javascript
parseFragmentsWithCache(source, resourcePath)[type] // 获取到 frags（前面提及）缓存代码的 type的内容
```



#### 2、style-loader.js?index=0&type=style!

```javascript
parseStyle({filePath: this.resourcePath, code: code, query: loaderQuery })
// {"filePath":"/Users/chenyong/Work/project/demo/src/DemoDetail/index.ux","code":"@import './demo.css';\n  .demo-page {\n    flex-direction: column;\n    justify-content: center;\n    align-items: center;\n  }\n\n  .title {\n    font-size: 40px;\n    text-align: center;\n  }","query":{"index":"0","type":"style"}}
```



* 合并css样式：processImport(code, curDir, log, depList) 

  ```javascript
  importCode = fs.readFileSync(importPath) // 根据路径读取内容
  mergeCode.replace() // 用importCode替换 "code":"@import './demo.css';
  ```

* 使用css 工具解析： css.parse(code, { silent: true })

  ```javascript
  ast = {"type":"stylesheet","stylesheet":{"rules":[],"parsingErrors":[]}}
  // ast.stylesheet.rules[0]
  { type: 'rule',
    selectors: [ '.test .abc' ],
    declarations: 
     [ { type: 'declaration',
         property: 'color',
         value: '#ff0000',
         position: [Object] },
       { type: 'declaration',
         property: 'background-color',
         value: '#ffff00',
         position: [Object] } ],
    position: 
     Position {
       start: { line: 2, column: 1 },
       end: { line: 5, column: 2 },
       source: undefined } }
  
  // type的类型：rule、font-face、keyframes
  ```



  - 校验属性值： 

    ```javascript
    // 校验属性值
    const camelCasedName = hyphenedToCamelCase(name)
    const subResult = validateDelaration(camelCasedName, value, { filePath })
    ```



  * 是否压缩属性名

    ```javascript
    compressCssAttr(jsonStyle)
    ```



#### 3、[json-loader](https://github.com/webpack-contrib/json-loader/blob/master/index.js)

```javascript
// 将json数据字符串化
module.exports = function (source) {
  if (this.cacheable) this.cacheable();

  var value = typeof source === "string" ? JSON.parse(source) : source;

  value = JSON.stringify(value)
    .replace(/\u2028/g, '\\u2028')
    .replace(/\u2029/g, '\\u2029');

  return `module.exports = ${value}`;
}
```

