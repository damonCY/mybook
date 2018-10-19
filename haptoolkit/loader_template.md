###  loader 解析template



```javascript
var $app_template$ = require(haptoolkit)
```



#### 解析script部分使用到的loader

- 1、Fragment-loader.js?index=0&type=template!
- 2、template-loader.js
- 3、json-loader



#### Fragment-loader.js?index=0&type=template!

```javascript
parseFragmentsWithCache(source, resourcePath)[type] // 获取到 frags（前面提及）缓存代码的 type的内容
```



#### template-loader.js

```javascript
parseTemplate({ code: source, filePath: this.resourcePath })
// 打印结果
{ code: '<!-- template里只能有一个根节点 -->\n  <div class="demo-page">\n    <text class="title">测试页面解析</text>\n  </div>',
  filePath: '/Users/chenyong/Work/project/demo/src/DemoDetail/index.ux' }
```





```javascript
// compiler/template/index.js
parse (source, callback) 
const doc = initParser(source.code, { treeAdapter: parse5.treeAdapters.default, locationInfo: true }) // 1、解析code 2、检查自闭合 是否满足xml标准 存在漏洞，自定义组件不能设置自闭合
	parser.parseFragment(code) // 使用parse5再次对code进行JSON化
// doc
{ nodeName: '#document-fragment',
  childNodes: 
   [ { nodeName: '#comment',
       data: ' template里只能有一个根节点 ',
       parentNode: [Circular],
       __location: [Object] },
     { nodeName: '#text',
       value: '\n  ',
       parentNode: [Circular],
       __location: [Object] },
     { nodeName: 'div',
       tagName: 'div',
       attrs: [Array],
       namespaceURI: 'http://www.w3.org/1999/xhtml',
       childNodes: [Array],
       parentNode: [Circular],
       __location: [Object] } ] }
    // 根据json数据判断根节合法性   
```



####  template-loader：traverse() 遍历节点 校验标签和标签属性

* validator.checkTagName(node, output)

  * 处理别名： image ==img

  * 组件标签

  * 根组件的合法性

  * 原子标签

  * 默认属性： 例如：lines

  * 根节点属性： 根节点不能使用if、 for、elif、else、show

  * 必须属性：item需要添加type

  * 属性的合法性：必须为文档提供的属性

  * 事件的合法性：必须为文档提供的事件

    ...

* Attrs = node.attrs 校验属性值

  * id

  * class

  * if

  * Else

  * elif

  * for

    ...

