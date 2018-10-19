## hap-toolkit工具编译过程



#### npm run release(webpack3)

```
/Users/chenyong/Work/project/test/demo01/node_modules/.bin/cross-env NODE_PLATFORM=na NODE_PHASE=ol  /Users/chenyong/Work/project/test/demo01/node_modules/.bin/webpack  --config  /Users/chenyong/Work/project/test/demo01/node_modules/hap-toolkit/tools/webpack.config.js
```



#### 开发环境(development)和生产环境(production)

 * NODE_PLATFORM=na
 * NODE_PHASE=ol

[Webpack3到webpack4之间的转换](http://git.xiaomi.com/#/c/640071/9/entry/webpack.config.js)



#### npm run release(webpack4)

```javascript
/Users/chenyong/Work/project/test/demo01/node_modules/.bin/cross-env NODE_PLATFORM=na NODE_PHASE=ol  /Users/chenyong/Work/project/test/demo01/node_modules/.bin/webpack  --config  /Users/chenyong/Work/project/test/demo01/node_modules/hap-toolkit/tools/webpack.config.js --mode production
```



[mode](https://webpack.docschina.org/concepts/mode/#mode-production)是webpack中独有的，有两种打包环境，一个是开发环境：`development`另外一个是生产环境：`production`